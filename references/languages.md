# Reference language selector

**When this applies:** you need a language version of this skill's references other than English.

Use an explicitly requested reference language first; otherwise use the language of the user's request. The default and fallback language is English.

| Language | Topic | Open only this reference |
| --- | --- | --- |
| Español (`es`) | stdin/stdout/stderr, pipelines, and the `-` operand | [Flujos y tuberías](es/streams-and-piping.md) |
| English (`en`) | stdin/stdout/stderr, pipelines, and the `-` operand | [Streams and Piping](streams-and-piping.md) |

If the requested language or topic has no bundled translation, use the matching English reference in the [context router](../SKILL.md#context-router). State that an English reference is being used because no translation is bundled for that language and topic. Do not invent a translated file or load every reference to choose a language.

Keep command names, flags, API identifiers, and example syntax exactly as documented. Reference language does not change the behavior of the program being designed.

Only the streams-and-piping topic currently has a Spanish translation. The English and Spanish files contain the same guidance; load one, not both, unless the task is translation review.

Reference language and response language are independent; follow the requested response language.
