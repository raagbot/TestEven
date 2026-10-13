# Contributing to Even Realities Feedback

Thanks for helping shape Even Realities. This board works best when requests are easy to find, easy to vote on and specific about the problem. This guide explains how to post and what happens next.

## Before you post

**Search existing requests.** Use the search box on the [Discussions](../../discussions) page with a few keywords. If you find a match:

- Upvote it with 👍. Votes are how we see what matters most.
- Add a comment with your own use case, especially if it differs from the original post.

Please don't open a duplicate. If you do, we'll label it **duplicate** and point you to the original so the votes add up in one place.

**Check it's a feature request.** For bugs and support, go to [Even Realities Support](https://support.evenrealities.com/hc/en-us) or email [support@evenrealities.com](mailto:support@evenrealities.com), where the support team can help faster. For general chat with other users and developers, join [our Discord](https://discord.gg/RbQnqJUuM).

## Writing a good request

**One idea per post.** If you have three ideas, open three posts. That way each one can be voted on, discussed and tracked separately.

**Write a clear title.** Say what you want in a few words, for example "Let apps run in the background" rather than "Suggestion" or "Please fix".

**Explain the problem.** The form asks for your request. A useful one answers:

- **What are you trying to do?** The goal, not just the feature.
- **What's in the way today?** What's missing, slow or confusing.
- **How often does this come up?** Daily, weekly, once in a while.
- **Who else would benefit?** Users of your app, other developers, a type of customer.

Screenshots, short videos and code snippets help, especially for developer requests.

**Pick the right category.** Choose the product area your request is about (for example "Even Hub Developer Platform & SDK" or "R1 Ring Hardware"), so the right team sees it.

## Posting from an AI agent

You can ask an AI agent to search the board, draft a request, post it, comment or upvote for you. Agents post through the GitHub API using **your** GitHub account, so everything they do appears under your name.

### Rules for agent posting (read these first)

Agents make it easy to post a lot, very quickly. Please don't. **Spam hurts everyone's requests**, because it buries real ones and makes votes meaningless.

1. **A human reviews every post.** Your agent drafts; you read and approve before anything is submitted. No unattended runs, loops, cron jobs or scheduled posting.
2. **Search before every new post.** If a matching request exists, upvote it and, if you have new information, add one comment. Don't open a new post.
3. **One idea per post, and no more than 3 new requests per person per day.**
4. **Don't bulk-post.** Don't turn a list, brainstorm, changelog, log file or chat transcript into a batch of requests.
5. **No auto-generated comments.** Use the 👍 reaction to show support. Only comment when you're adding something new.
6. **Use the same structure as the web form** (see the template below), including a category.
7. **Disclose it.** End agent-drafted posts with: `Drafted with an AI agent and reviewed by @your-username`.

We remove posts that break these rules. Accounts that repeatedly do so will be blocked from the board.

### Setup

1. Install the [GitHub CLI](https://cli.github.com/) and sign in with `gh auth login`. Your agent can then run the commands below. Agents that use the [GitHub MCP server](https://github.com/github/github-mcp-server) can do the same through its discussion tools.
2. Never paste your GitHub token into a post, comment or screenshot.

Posting through the API skips the web form, so no category or label is applied automatically. Follow the template below so the team can triage your request.

### 1. Search existing requests

```bash
gh api graphql -f query='
query($q: String!) {
  search(query: $q, type: DISCUSSION, first: 10) {
    nodes { ... on Discussion { number title url upvoteCount } }
  }
}' -f q='repo:even-realities/feedback is:discussion background apps'
```

Replace `background apps` with a few keywords. If a match comes back, upvote it (step 4) instead of posting.

### 2. Look up the board and category IDs

```bash
gh api graphql -f query='
query {
  repository(owner: "even-realities", name: "feedback") {
    id
    discussionCategories(first: 10) { nodes { id name } }
  }
}'
```

Note the repository `id` and the `id` of the **Feature Requests** category.

### 3. Post a new request

```bash
gh api graphql -f query='
mutation($repo: ID!, $cat: ID!, $title: String!, $body: String!) {
  createDiscussion(input: {repositoryId: $repo, categoryId: $cat, title: $title, body: $body}) {
    discussion { url }
  }
}' -f repo='REPOSITORY_ID' -f cat='CATEGORY_ID' \
   -f title='Let apps run in the background' \
   -f body="$(cat request.md)"
```

Use this structure for `request.md`:

```markdown
### Your request
What you'd like us to build or improve, and why it matters to you.

- What are you trying to do?
- What's in the way today?
- How often does this come up?
- Who else would benefit?

### Category
Even Hub Developer Platform & SDK

---
Drafted with an AI agent and reviewed by @your-username
```

Pick the category from the list in the [README](README.md#request-categories).

### 4. Upvote or comment on an existing request

Get the request's ID from its number, then upvote it:

```bash
gh api graphql -f query='
query { repository(owner: "even-realities", name: "feedback") { discussion(number: 42) { id } } }'

gh api graphql -f query='
mutation($id: ID!) { addUpvote(input: {subjectId: $id}) { subject { upvoteCount } } }' -f id='DISCUSSION_ID'
```

To add a comment with new information:

```bash
gh api graphql -f query='
mutation($id: ID!, $body: String!) {
  addDiscussionComment(input: {discussionId: $id, body: $body}) { comment { url } }
}' -f id='DISCUSSION_ID' -f body='Our use case: ...'
```

### Instructions you can give your agent

Copy this into your agent's instructions or prompt:

> When posting to github.com/even-realities/feedback: search existing discussions first and upvote instead of posting a duplicate. Draft one feature request at a time using the template in CONTRIBUTING.md, show me the draft, and only submit after I approve it. Never post more than 3 new requests in a day, never post comments that only say "+1", and never post on a schedule or in a loop. End every post with "Drafted with an AI agent and reviewed by @my-username".

## What happens after you post

1. Your request starts as **Under Review**.
2. We read every post. If we need details, we'll ask in the comments and label it **Need more Info**; please reply so we can keep it moving.
3. When we decide to build something, it moves to **Planned**, then **In Progress** while our team works on it.
4. When it's available, we mark it **Shipped** and link to the release notes or docs.

Some requests end differently:

- **Declined**: we've decided not to build it, and we'll explain why.
- **Unable to Validate**: we couldn't confirm or reproduce what's described.
- **duplicate**: it's already been requested, and we'll link the original.

Subscribe to a request (the **Subscribe** button on the right of the page) to get notified when its status changes.

Votes are an important signal, but we also weigh feasibility, our roadmap and how many people a change helps, so the most-voted request isn't always built first. We'll explain our reasoning where we can.

## Community guidelines

- Be respectful. Critique ideas, not people.
- Stay on topic. Keep comments about the request in the thread.
- No spam, self-promotion or sharing private information. This includes agent-generated spam (see [Posting from an AI agent](#posting-from-an-ai-agent)).
- Don't post security vulnerabilities publicly; email [support@evenrealities.com](mailto:support@evenrealities.com) instead.

We'll edit or remove posts that break these guidelines.

## Questions about this board

Ask in [our Discord](https://discord.gg/RbQnqJUuM) and tag the Developer Relations team.
