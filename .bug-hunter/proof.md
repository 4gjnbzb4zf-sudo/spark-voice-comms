<!-- sentinel:link-proof -->
### 🔗 Link Proof

_Click through to see exactly what this PR changes, without rebuilding the hunt context._

- **Offending line (upstream)**: [src/voice_comms_chip/spark_hook.py:886](https://github.com/vibeforge1111/spark-voice-comms/blob/main/src/voice_comms_chip/spark_hook.py#L886)
- **Severity**: 🟢 LOW
- **Finding ID**: `SILE-178`
- **Category**: `silent-failure`
- **Detector**: `silent-failure` (sentinel-engine)
- **Discovered**: 2026-05-24

<!-- sentinel:link-proof -->

## Log the spark hook voice comms chip failure instead of swallowing it

This <code>except</code> block at <code>src/voice_comms_chip/spark_hook.py:886</code> drops the exception and continues. Operators investigating a failure read every log line in the path and find nothing. The clue lives only in the caller's return value, which the caller usually treats as fine.

### 🔴 Before

`src/voice_comms_chip/spark_hook.py:886`

```python
        try:
            env_map.update({key: value for key, value in _read_env_map(env_file_path=env_file_path).items() if value})
        except Exception:
            pass
```

### 🟢 After

```python
        try:
            env_map.update({key: value for key, value in _read_env_map(env_file_path=env_file_path).items() if value})
        except Exception as exc:
            logging.getLogger(__name__).warning("failed to merge env file %s: %s", env_file_path, exc)
```

### 🔬 Evidence

| Field | Value |
|---|---|
| File | `src/voice_comms_chip/spark_hook.py:886` |
| Category | `silent-failure` |
| Severity | 🟢 LOW |
| Detector | `silent-failure` |
| Discovered | 2026-05-24 |

---
Tested with the existing test fixtures; nothing regressed. Drop the proof commit before merging if you don't want the <code>.bug-hunter/</code> directory in <code>main</code>.
