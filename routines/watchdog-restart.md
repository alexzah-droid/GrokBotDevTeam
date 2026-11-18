# Watchdog recovery

Enable the routine only when the user chooses it. Lead owns it. Trigger at host startup or, if unsupported, run an approved check every minute.

Copy the first five argv items from the startup rule. Add `--actor controller service start`:

```text
<installed-python> -B <release>/teamctl.py --workspace <runtime> --actor controller service start
```

Check `status.watchdog_alive` after startup. Report failures as blockers. [Operations](../docs/OPERATIONS.md#watchdog).
