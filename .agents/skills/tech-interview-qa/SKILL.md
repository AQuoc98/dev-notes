---
name: tech-interview-qa
description: "Process technical interview Q&A from an article/YouTube URL or user-supplied questions, compare it with one target Markdown file, rewrite clear interview-ready content, flag related or duplicate questions, and update only after explicit approval. Use for interview-document tasks in this repository."
---

# Tech Interview Q&A

Use this skill when the user wants to add or improve technical interview questions in a Markdown document in this repository. The user can provide either:

- an article or YouTube URL; or
- one or more questions they collected, optionally with rough answers or notes.

For user-facing prompt examples, see the [Tech Interview Q&A skill guide](../../../ai-workflow/06-tech-interview-qa-skill.md). Follow the repository's `AGENTS.md` instructions as the execution contract.

## Input and target handling

- Detect the input mode from the user's message: URL or self-collected questions.
- Read `Tài liệu đích` or `TARGET_FILE` when provided. If it is omitted, use `interview.md`.
- Require one concrete Markdown target. The target must exist unless the user explicitly asks to create it.
- Inspect only the declared target file for duplication and updates. Do not infer another target or modify unrelated Markdown files.
- A concise prompt is enough. Do not require the user to repeat the full workflow instructions.

Examples of valid concise prompts:

```text
Phân tích URL này cho interview Q&A, target: typescript.md
https://example.com/article
```

```text
Thêm các câu hỏi sau vào interview.md:
1. What is event delegation?
2. When should I use a message queue?
```

## Phase 1 — proposal only

Unless the user is clearly approving a previously reviewed proposal, do not edit any file. Perform a read-only analysis:

1. Read the entire target file.
2. For a URL, access the article/transcript reliably and summarize the source in three to five concise points. If it cannot be accessed reliably, ask for the transcript or relevant content and do not invent source claims.
3. For self-collected questions, summarize the received questions and any supplied answers/notes. Treat them as raw input, not verified facts.
4. For every submitted question, classify it as `Mới`, `Liên quan`, or `Trùng lặp`.
5. Identify related or duplicate content in the target, naming the existing heading/question and explaining the overlap.
6. Rewrite each question and answer so it is clear, technically accurate, interview-appropriate, and consistent with the target file's style. If only a question was supplied, draft an answer and label that explanation as Codex-added in the proposal.
7. For `Liên quan` or `Trùng lặp` items, recommend one action: add, extend, merge, or skip. Never silently overwrite existing content.

Present the proposal in this order:

1. `Summary / Input Interpretation`
2. `Conflict & Duplication Check`, with a status for every submitted question
3. `Editing Notes`
4. `Proposed Q&A`, formatted as Markdown that can be inserted into the target file

End Phase 1 with:

> Bạn có đồng ý với bản tóm tắt và danh sách Q&A đề xuất trên không? Vui lòng xác nhận hoặc cho biết các điểm cần chỉnh sửa/bổ sung trước khi tôi cập nhật trực tiếp vào file tài liệu.

If the user asks to revise the proposal without a clear approval, revise only the proposal and remain in Phase 1.

## Phase 2 — update after approval

Proceed only after a clear approval such as `APPROVE`, `APPROVE ...`, or `Đồng ý cập nhật vào file` referring to the current proposal and target.

- Modify only the declared target file.
- Apply the approved action for each question: add, extend, merge, or skip.
- Preserve the target's existing Markdown structure, language, and style.
- Do not remove unrelated content or add duplicate Q&A.
- Re-read the updated target and inspect the resulting diff.
- Report what was added, revised, merged, or skipped, including any remaining uncertainty.

Approval applies only to the reviewed proposal and requested scope. If the target changes after Phase 1, re-read the new target and repeat the duplication check before applying any update.
