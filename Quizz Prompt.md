

 
Turn part of this conversation into an active-recall note for Obsidian.

Topic: [TOPIC, or blank]

## Step 1: Check with me first. Do not write the note yet.

If the topic is blank, list the candidate topics from this conversation as a numbered list, ask me to pick one, and stop.

Once there is a topic, reply with:

1. **What the note will cover:** 2-3 sentences on the problem and the takeaway.
2. **Issues to settle (only if there are any):** look for mistakes, half-truths, and anything vague or half-remembered, in what I said and in what you said earlier. For each one, show:
   - what was said
   - what is wrong or incomplete
   - why, using the code from this conversation as evidence where possible

Then stop and wait for my reply, even if you found no issues. Discuss any issues with me until we agree. Only write the note once I've confirmed.

## Step 2: Write the note

Output only the note, in one Markdown code block, using this structure:

```
# [Title]

**Context:** [The setup]

**Question:** [The question]

> [!answer]- Answer
> [Answer]
>
> **Why:** [Why]
>
> **Principle:** [Principle]
>
> **When this doesn't apply:** [Limits]
```

Rules:

**Hiding the answer**
- The title, Context and Question must never name or hint at the solution, principle or pattern. They should work as a real test.
- Title the note after the problem ("Where does token caching belong?"), not the solution ("Session service pattern").
- Everything after the Question goes inside the callout. Every line in it must start with `>`, including blank lines and lines inside code blocks.

**Sections**
- Only Question and Answer are required.
- Include Context, Why, Principle and "When this doesn't apply" only when they add something real. Otherwise leave them out completely, with no placeholders or "N/A".
- Why should explain what goes wrong with the tempting alternative.
- Principle should give the name of the principle or pattern, and only if it truly applies to this code.

**Voice**
- Speak directly to me, like someone quizzing me and then giving me the answer: "You have a JWT service... where would you put this?" then "You should create..."

**Content**
- Use my real code and names from this conversation.
- End the Answer with the general rule, so it carries over to other projects.
- Include only what we agreed on in Step 1.

**Length**
- Don't pad: never stretch two sentences into a paragraph, and don't add intros or recaps.
- Use examples and code snippets whenever they make the idea clearer.
