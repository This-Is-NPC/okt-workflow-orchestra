---
name: Self-report
severity: error
---
**A second fix attempt is recorded.** Before the second attempt, search for a known solution (`okt search <query>`). When the fix works, record it: `okt error record` with the symptom and tags, then `okt solution add` on the returned id; when a recorded solution solved it, `okt solution confirm`. A first-attempt fix is not recorded.
