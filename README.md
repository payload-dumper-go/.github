# .github
payload-dumper-go extracts the partition images packed inside an Android payload.bin. That file is the core of many full OTA and factory packages. The tool writes boot, vendor, system, and the other images out as separate files you can inspect or hand to a flasher that wants them one by one.
