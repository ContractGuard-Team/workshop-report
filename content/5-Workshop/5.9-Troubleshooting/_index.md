---
title: "Troubleshooting and lessons learned"
date: 2026-01-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

#### Issues met and how they were solved

| Issue | Cause | Fix |
| --- | --- | --- |
| Containers report `NoCredentialsError` while `aws` works on the host | IMDS hop limit = 1 blocks requests coming through the bridge network | Set `HttpPutResponseHopLimit=2`; `preflight.sh` checks STS from inside a container |
| GitHub Push Protection rejected the push | The Gemini key was hard-coded in `common/settings.py` | Removed the key from code, read only `GEMINI_API_KEY` from `.env` (gitignored), rewrote the unpushed commit, rotated the key |
| Gemini returns 400 on the second tool round | Gemini 3 requires each `functionCall`'s `thoughtSignature` to be sent back | The client keeps the signature in the tool call and the agent puts it back in the transcript |
| `MALFORMED_FUNCTION_CALL` | The prompt told the model to call a tool on a turn with no tools offered | Only ask for tools when the agent has them (Agents 2/3) |
| DynamoDB progress went backwards from `agent_3` to `agent_1` | The `reflect` span (long-term memory) after Agent 3 counted as a stage | Monitoring ignores reflect spans when computing progress |
| 429 even for short answers | The provider counts `max_output_tokens` against the tokens-per-minute limit as soon as the request arrives | Size `max_completion_tokens` per agent (32k/64k/32k) |
| systemd could not read the source | `ProtectHome=true` hides `/home` | Clone into `/opt/contract-risk-management` |
| An LLM agent could read the web app's business tables through Supabase | App tables shared the `public` schema and the secret key bypasses RLS | App tables moved to the `app` schema; MCP only exposes the tables in `SUPABASE_TABLES` |
| Changing the embedding model broke search | Vector size differed from the old Qdrant collection | Recreated a 1024-d + sparse collection and re-indexed; documented "new tokenizer = re-index" |

#### Lessons learned

* Check permissions **from inside the container**, not just on the host.
* Never write a secret into code, even "just for a test"; enable secret scanning from day one.
* LLM output is untrusted data: always verify it in code (schema, verbatim quotes, code-computed scores).
* Delete the SQS message only as the very last step, after the result is durably stored.

#### Future work

* Move the worker to Amazon ECS and scale on queue depth.
* Put EC2 in a private subnet with Interface Endpoints and a NAT Gateway when the budget allows.
* Infrastructure as code (AWS CDK or Terraform) and CI/CD with GitHub Actions using OIDC.
* A quality evaluation set (finding recall/precision) on labelled contracts.
