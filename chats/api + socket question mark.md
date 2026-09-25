


---

- reference : https://chatgpt.com/c/5584dca1-c4c4-832b-8e10-513d796f96d2

i have this ans i was wondering could i instead made the consume-drink an api and from there i emit the socket of drink consumed ? is that feasable ? 
  @UseGuards(WsRolesGuard)
  @Roles(Role.WAITER)
  @SubscribeMessage('consume-drink')
  async consumeDrink(
    @ConnectedSocket() socket: authSocket,
    @MessageBody() payload: { code: string },
  ) {
    try {
      if (!payload.code) throw new WsException('No code provided');

      const response = await this.ladiesNightService.consumeDrink(payload.code);

      socket.emit('drink-consumed', response);

      if (response.userSocketId)
        this.server.to(response.userSocketId).emit('drink-consumed', response);
    } catch (error) {
      socket.emit('drink-consumed', { success: false, error: error.message });
    }
  }


---

Yes, and in most production systems I would actually prefer that approach.

Right now your architecture is:

```
Client
  -> Socket event: consume-drink
      -> Service
          -> Socket emits result
```

You could change it to:

```
Waiter App
  -> POST /ladies-night/consume-drink
      -> Service
          -> Emit socket event(s)
      -> Return HTTP response
```

Something like:

```ts
@Post('consume-drink')
@Roles(Role.WAITER)
async consumeDrink(@Body() dto: ConsumeDrinkDto) {
  const response = await this.ladiesNightService.consumeDrink(dto.code);

  if (response.userSocketId) {
    this.gateway.server
      .to(response.userSocketId)
      .emit('drink-consumed', response);
  }

  return response;
}
```

or even better:

```ts
const response = await this.ladiesNightService.consumeDrink(dto.code);

this.eventEmitter.emit(
  'ladies-night.drink-consumed',
  response,
);

return response;
```

Then the gateway listens:

```ts
@OnEvent('ladies-night.drink-consumed')
handleDrinkConsumed(data: DrinkConsumedEvent) {
  if (data.userSocketId) {
    this.server
      .to(data.userSocketId)
      .emit('drink-consumed', data);
  }
}
```

From an architecture perspective, I would avoid putting socket emission directly inside the service.

Bad:

```ts
Service
  -> gateway.emit(...)
```

because now your business logic depends on websocket infrastructure.

Better:

```ts
Controller
  -> Service
  -> Emit event
```

Best (for larger systems):

```ts
Controller
  -> Service
      -> Domain Event
  -> Event Handler
      -> Socket Gateway
```

For your Volto app, if drink consumption is initiated by a waiter pressing a button in the admin panel, HTTP is probably the cleaner choice. WebSockets are more valuable for the _notification_ part ("drink consumed", points updated, quota changed, etc.) than for the command itself.

So yes, it is absolutely feasible, and I'd probably make `consume-drink` an HTTP endpoint while keeping the `"drink-consumed"` notification as a socket event.