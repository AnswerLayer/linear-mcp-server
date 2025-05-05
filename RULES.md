# Linear Workflow & Tool Usage Rules

## I. General Principles & Mindset

*   **Stay Updated:** Check Linear task state often to stay on track. Use `linear_search_issues` (for team/project tasks) if needed.
*   **Get Context First:**
    *   **Organization Info (`linear_get_organization_details`):** Use this tool *early* or *whenever needed* to find essential IDs like `teamId` or `userId` if they are unknown and required for other tools (`create_issue`, `search_issues`).
    *   **Issue Details (`linear_get_issue`):** When a task identifier (e.g., `PROJ-123`) is mentioned or known, **ALWAYS** use `linear_get_issue` to retrieve the *full* issue details, **including comments**, before taking further action on that specific issue.
*   **Search Before Creating (`linear_search_issues`):** Before creating a new task, use `linear_search_issues` with relevant keywords or identifiers to check if a similar task already exists. This helps avoid duplicates. Use specific queries and filters for best results.
*   **Record & Track:** Use Linear tools consistently to record work plans and track progress against them.
*   **Consistency:** Use consistent naming conventions for tasks.
*   **Avoid Duplication:** Do not create duplicate tasks or comments (reinforces searching first).
*   **Error Handling Note:** Tasks appearing as "interrupted" likely indicate an underlying error (e.g., bad request). Investigate rather than assuming user interruption.

## II. Task Planning & Creation (`linear_create_issue`)

*   **Permission:** **Ask user permission before creating NEW tasks.**
*   **Tool:** Use `linear_create_issue`.
    *   Requires `title` and `teamId` (use `linear_get_organization_details` if `teamId` is unknown).
    *   Always include: `description`, `priority` (0=None, 1=Urgent, 2=High, 3=Medium, 4=Low), `status`, `parentId`.
*   **Content:**
    *   Write clear, actionable titles.
    *   Write detailed, well-thought-out descriptions using Markdown.
    *   Descriptions **must** be outcome-oriented.
    *   Descriptions **must** include acceptance criteria.
*   **Organization:** Use `parentId` to link related tasks and maintain organization.

## III. Working on Tasks (`linear_update_issue`, `linear_add_comment`, `run_terminal_cmd`)

*   **Starting a Task:**
    *   Announce which task is being started, using identifier like `PROJ-123` and issue title.
    *   Update status to `In Progress` using `linear_update_issue` (requires `id` - can be UUID or identifier).
    *   **Branching:** Create a new branch from `staging` (or your team's integration branch) using the convention `feature/<ticket>-<desc>` or `bugfix/<ticket>-<desc>` (e.g., `feature/PROJ-123-add-login`). Use `run_terminal_cmd`. (Hotfixes branch from `main` or your production branch).
*   **During Progress:**
    *   **Git Commits:** Make small, frequent Git commits using `run_terminal_cmd`. Each commit should represent a logical unit of work.
        *   Commit messages **must** follow Conventional Commits: `<type>[optional scope]: <description> [optional linear-issue]`.
        *   Common types: `feat`, `fix`, `build`, `chore`, `ci`, `docs`, `style`, `refactor`, `perf`, `test`.
        *   **Reference the Linear issue identifier** in the commit message (e.g., `feat: Implement user login [PROJ-123]`).
    *   **Linear Updates:** Report progress regularly using `linear_add_comment` (requires `issueId` - can be UUID or identifier), especially after significant commits or reaching milestones.
    *   Update status via `linear_update_issue` as appropriate (e.g., if blocked, though usually stays `In Progress`).
    *   Add a comment any time status is changed.
    *   **Do NOT change title or description** once `In Progress`. Use `linear_add_comment` for all updates, context, or changes in direction.
    *   If implementation deviates from the original plan, explain the deviation using `linear_add_comment`.
*   **Permission (Updates):** **Do NOT ask user permission** for routine updates to *existing* tasks (e.g., setting status, changing priority, adding comments, updating description *before* starting).
*   **Continue Working UNLESS:**
    *   A strategic decision point is reached.
    *   More context is needed from the user.
    *   The same error occurs 4 times consecutively.
    *   Confusion arises about the task or next steps.
    *   User testing or input is required to proceed.

## IV. Completing Tasks (`linear_update_issue`, `linear_add_comment`)

*   **Validation:** Thoroughly test and validate that all acceptance criteria are met **before** marking as done.
*   **Marking Done:**
    *   Update status to `Done` using `linear_update_issue` (requires `id` - can be UUID or identifier).
    *   **ALWAYS** add a final comment using `linear_add_comment` (requires `issueId` - can be UUID or identifier) when marking `Done`. This comment **must**:
        *   Explain what was done.
        *   Note any changes from the original plan or important context for future reference.
*   **Dopamine:** Enjoy the satisfaction of completing a task!
*   **Next Steps:** After marking `Done` and commenting, **pause** and check in with the user:
    *   Summarize the work completed.
    *   Suggest potential next steps or ask what to work on next.

## V. Other Actions & Statuses

*   **Status Definitions:**
    *   **Backlog:** An idea worth noting but not worth working on soon.
    *   **To do:** Something that should be done soon.
    *   **In Progress:** Actively working on.
    *   **Done:** Work complete, all acceptance criteria have been met, no more action to take.
    *   **Cancelled:** An idea that was not completed and will not be revisited. Use status update via `linear_update_issue`.
    *   **Duplicate:** Too similar to another issue; the other issue will be used. Use status update via `linear_update_issue`. Add comment linking to the primary issue.
*   **Getting User-Specific Issues (`linear_get_user_issues`):**
    *   Use this tool to retrieve issues assigned to a specific user (or the authenticated user if `userId` is omitted).
    *   Useful for checking personal workload or reviewing assignments.
*   **Archiving (`linear_archive_issue`):**
    *   Use this tool to archive (soft-delete) an issue that is no longer relevant but should be kept for historical purposes. ONLY DO THIS WHEN TOLD
    *   Requires the issue `id` (can be UUID or identifier).
