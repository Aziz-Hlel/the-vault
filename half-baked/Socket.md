



- sockets have a concept of rooms, usually you on app start app you want the user to connect directly to socket , there you make the implementation to join him to the corrects rooms, a default one is a room specifically for that user alone, to send him private messages as an example and private notifications, you can add him to rooms of groups he's in, add him to a room based on his role if it's admin he get notifications that all admins need to receive, you implement this in the `connection` this is where the handshake happens, the first time the client secure a connection with the server, example :
```ts
const userRoom = (userId: string) => `user:${userId}`;
const ADMINS_ROOM = 'admins';

class SocketProvider {
this.io.use(socketAuth);

this.io.on('connection', (socket) => {
      // every user gets a private room, so we can reach all their devices
      socket.join(userRoom(socket.data.user.id));

      if (allAdminRoles.some((role) => role === socket.data.user.role)) {
        socket.join(ADMINS_ROOM);
      }
    });
}
```