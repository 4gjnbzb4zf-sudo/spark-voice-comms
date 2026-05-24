<!-- sentinel:link-proof -->
### 🔗 Link Proof

_Click through to see exactly what this PR changes, without rebuilding the hunt context._

- **Offending line (upstream)**: [src/voice_comms_chip/spark_hook.py:1814](https://github.com/vibeforge1111/spark-voice-comms/blob/main/src/voice_comms_chip/spark_hook.py#L1814)
- **Severity**: 🟢 LOW
- **Finding ID**: `SILE-590`
- **Category**: `silent-failure`
- **Detector**: `silent-failure` (sentinel-engine)
- **Discovered**: 2026-05-24

<!-- sentinel:link-proof -->

## Stop dropping spark hook voice comms chip errors without a log line

The exception handler at <code>src/voice_comms_chip/spark_hook.py:1814</code> catches the error and returns a failure-shaped result without logging. If this fires in production, you'll never see it in the application's own logs — only the downstream consumer notices, usually as silent data loss.

### 🔴 Before

`src/voice_comms_chip/spark_hook.py:1814`

```python
    except Exception:
        return None
    voices = payload.get("voices") if isinstance(payload, dict) else None
```

### 🟢 After

```python
    except Exception:
        logging.getLogger(__name__).debug(
            "Failed to resolve ElevenLabs fallback voice id", exc_info=True
        )
        return None
    voices = payload.get("voices") if isinstance(payload, dict) else None
```

### 🔬 Evidence

| Field | Value |
|---|---|
| File | `src/voice_comms_chip/spark_hook.py:1814` |
| Category | `silent-failure` |
| Severity | 🟢 LOW |
| Detector | `silent-failure` |
| Discovered | 2026-05-24 |

---
No behavioral change beyond the surfaced log line. Happy to split this if you'd prefer separate PRs per call site.
