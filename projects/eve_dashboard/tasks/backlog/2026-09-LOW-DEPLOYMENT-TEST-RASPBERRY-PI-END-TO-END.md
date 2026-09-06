---
status: backlog
priority: LOW
type: documentation
system_domain: DEPLOYMENT
mvp_alignment: OPERATIONAL_READINESS
local_worker_safe: false
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch (after user provides Raspberry Pi hardware).**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent** + **Hardware Integration Specialist**.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-LOW-DEPLOYMENT-TEST-RASPBERRY-PI-END-TO-END.md

PREREQUISITES (HUMAN MUST VERIFY BEFORE DISPATCH):
  - [ ] Raspberry Pi 3+ is available and connected to network
  - [ ] Pi has 512MB+ free disk space (checked: df -h)
  - [ ] Pi has Ethernet or WiFi connectivity to reach:
    - GitHub (to clone eve-dashboard repo)
    - Docker Hub (to pull base image)
    - Internet (to verify time sync via NTP)
  - [ ] Human has SSH access to Pi: ssh pi@<RASPBERRY_PI_IP>
  - [ ] This task file is moved to active/ folder and status changed to: active

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/eve_dashboard/tasks/backlog/2026-09-LOW-DEPLOYMENT-TEST-RASPBERRY-PI-END-TO-END.md \
         projects/eve_dashboard/tasks/active/2026-09-LOW-DEPLOYMENT-TEST-RASPBERRY-PI-END-TO-END.md
  Then edit the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

EXECUTION ENVIRONMENT:
  - Host machine: This Mac (where eve-dashboard repo is cloned)
  - Target machine: Raspberry Pi on same network (or accessible via SSH)
  - Communication: SSH from Mac to Pi for commands
  - Artifact preservation: Logs copied back to host for review

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT NOTE**: This task requires BOTH host machine (Mac) AND target hardware (Raspberry Pi).
Agent will coordinate between them via SSH. Not fully automatable from local Docker.

---

# TASK: Test EVE Dashboard on Raspberry Pi — End-to-End Deployment

**Status**: BACKLOG  
**Priority**: LOW  
**Type**: documentation + operational testing  
**Created**: 2026-09-04  
**Last Updated**: 2026-09-04  
**Requires**: Raspberry Pi 3+ hardware  

---

## Summary

This task validates that EVE Dashboard deploys successfully to a Raspberry Pi and runs 24/7 with systemd service management. It's a hybrid task: part automation (running setup scripts), part documentation (capturing gotchas and Pi-specific tuning).

**Note**: This is a **LOW priority backlog task** intended for after user has real credentials and wants persistent 24/7 operation. Can be skipped if user only needs local testing on Mac.

---

## Problem Statement

### Current State
- ✅ Docker image builds on Mac
- ✅ Container starts on Mac
- ✅ All tests pass
- ⏳ **Unknown**: Does it work on Raspberry Pi? (Different CPU arch, RAM constraints, SD card I/O)

### Success Criteria
- [ ] Repository clones successfully on Pi
- [ ] Docker installs on Pi (using official Pi Docker install)
- [ ] `bash setup-raspberrypi.sh` completes without errors
- [ ] Docker image builds on Pi (may take 10-15 min due to ARM architecture)
- [ ] Container starts and health checks pass
- [ ] Systemd service is enabled and starts automatically on reboot
- [ ] Service logs are accessible via `journalctl -u eve-dashboard`
- [ ] Application responds on http://raspberrypi.local:8765
- [ ] 24-hour soak test: service runs continuously without restart loops or OOM kills
- [ ] Document any Pi-specific gotchas (CPU throttling, SD card performance, memory tuning)

---

## Environment Requirements

### Hardware Prerequisites
- **Raspberry Pi Model**: 3B+, 4, or 5 (Pi 3B minimum, but tight on RAM)
- **Storage**: 16GB+ SD card (8GB theoretical minimum, but 16GB recommended)
- **Memory**: 
  - Pi 3: 1GB total (docker container limited to 256MB per docker-compose.yml)
  - Pi 4: 2-4GB total (can increase docker container to 512MB)
  - Pi 5: 4-8GB total (can increase docker container to 1GB)
- **Network**: Ethernet (recommended) or WiFi with stable connection
- **Power**: Adequate 5V supply (reboots during build if underpowered)

### Software Prerequisites
- **OS**: Raspberry Pi OS (Bullseye or later; Debian-based)
- **SSH**: Enabled and accessible from Mac
- **Time Sync**: NTP configured (docker build fails if time skewed >2 hours)
- **Disk Free**: At least 500MB free before starting

---

## Architecture Overview

### Deployment Topology
```
┌─────────────────────────────────┐
│         Mac Host                │
│  /Users/tam0013/Documents/      │
│  git/eve-dashboard/             │
│  (source repository)            │
└────────────┬────────────────────┘
             │ git clone / git push
             │
┌────────────┴────────────────────┐
│    Raspberry Pi                 │
│  ~/eve-dashboard/               │
│  (cloned repo)                  │
│                                 │
│  ┌──────────────────────────┐   │
│  │ Docker Container         │   │
│  │ (Python 3.11 + FastAPI)  │   │
│  │ Port 8765:8765           │   │
│  │ CPU Limit: 2 cores       │   │
│  │ RAM Limit: 256-512MB     │   │
│  └──────────────────────────┘   │
│                                 │
│  ┌──────────────────────────┐   │
│  │ Systemd Service          │   │
│  │ eve-dashboard.service    │   │
│  │ Enabled + RestartAlways  │   │
│  └──────────────────────────┘   │
│                                 │
│  ┌──────────────────────────┐   │
│  │ Persistent Storage       │   │
│  │ ~/eve-dashboard/data/    │   │
│  │ (SQLite, logs, backups)  │   │
│  └──────────────────────────┘   │
└──────────────────────────────────┘
```

### What Happens During Deployment
1. **SSH from Mac to Pi**: Agent runs commands on Pi via `ssh pi@<IP>`
2. **Clone repository**: User must clone eve-dashboard to Pi (or sync via git pull)
3. **Run setup script**: `bash setup-raspberrypi.sh` automates:
   - Verify Docker is installed (if not, install via official Pi Docker install)
   - Build Docker image (first time: 10-15 min, ARM architecture compilation)
   - Install systemd service file to `/etc/systemd/system/eve-dashboard.service`
   - Enable service: `sudo systemctl enable eve-dashboard`
   - Start service: `sudo systemctl start eve-dashboard`
4. **Verify operation**:
   - Container runs with health checks
   - Logs written to `/home/pi/eve-dashboard/data/logs/dashboard.log`
   - Service auto-restarts if killed/crashed
   - Service auto-starts on Pi reboot

---

## Implementation Steps

### Step 1: Pre-Deployment Checklist (Human on Pi)

**Agent guides human through these checks via SSH:**

```bash
# On Raspberry Pi
uname -m                          # Should show: armv7l (Pi 3) or aarch64 (Pi 4/5)
df -h                            # Should show: 500MB+ free in /home partition
free -h                          # Should show: available RAM (at least 256MB)
timedatectl status               # Should show: "System clock synchronized: yes"
ping 8.8.8.8                     # Should succeed (network connectivity)
which docker                     # Should show: /usr/bin/docker (or not installed yet)
```

**Result**: If any check fails, agent logs issue and halts (user must fix)

### Step 2: Repository Setup on Pi (Human Responsibility)

```bash
# On Raspberry Pi, user must do this once:
cd ~
git clone https://github.com/USER/eve-dashboard.git
# Or if already cloned:
cd ~/eve-dashboard && git pull origin main
```

**Note**: Agent cannot do this (no GitHub auth on Pi). User clones or syncs manually.

### Step 3: Run Automated Setup Script (Agent Guides)

**Agent runs via SSH from Mac:**

```bash
# From Mac host, SSH to Pi:
ssh pi@<RASPBERRY_PI_IP> "cd ~/eve-dashboard && bash setup-raspberrypi.sh"
```

**What setup-raspberrypi.sh does** (already written in eve-dashboard repo):
1. Check Docker installed; if not, run Docker install script
2. Build image: `docker build .` (will take 10-15 min on Pi)
3. Copy systemd service file: `sudo cp eve-dashboard.service /etc/systemd/system/`
4. Reload systemd: `sudo systemctl daemon-reload`
5. Enable service: `sudo systemctl enable eve-dashboard`
6. Start service: `sudo systemctl start eve-dashboard`
7. Print service status: `systemctl status eve-dashboard`

**Expected output**: "active (running)" status for eve-dashboard service

### Step 4: Verify Container Startup (Agent Checks)

**Agent runs via SSH:**

```bash
# Check service status
ssh pi@<IP> "sudo systemctl status eve-dashboard"
# Expected: active (running)

# Check logs
ssh pi@<IP> "sudo journalctl -u eve-dashboard -n 50"
# Expected: Clean startup, no exceptions

# Check container health
ssh pi@<IP> "docker ps"
# Expected: eve-dashboard container RUNNING with "healthy" status

# Check port binding
ssh pi@<IP> "curl -s http://localhost:8765/health"
# Expected: 200 OK (or 404 if /health endpoint doesn't exist, but connection succeeds)

# Check application logs
ssh pi@<IP> "tail -20 ~/eve-dashboard/data/logs/dashboard.log"
# Expected: Clean startup logs, no stack traces
```

### Step 5: Load Credentials on Pi (User Responsibility)

**Agent guides user to:**

```bash
# On Pi, via SSH or direct access:
nano ~/eve-dashboard/config/credentials.env
# User enters: EVE_CLIENT_ID=<your ID>
#              EVE_CLIENT_SECRET=<your secret>
#              EVE_REDIRECT_URI=http://raspberrypi.local:8765/callback
# Save with Ctrl+X, Y, Enter
```

**Note**: Credentials file should NEVER be committed to git (already in .gitignore)

### Step 6: Restart Service with Credentials (Agent or User)

```bash
# Restart service to pick up new credentials
ssh pi@<IP> "sudo systemctl restart eve-dashboard"

# Wait 5 seconds
sleep 5

# Check logs for auth errors
ssh pi@<IP> "tail -20 ~/eve-dashboard/data/logs/dashboard.log"
# Expected: No "SSO" or "auth" errors
```

### Step 7: Test Application Access (Agent Verifies)

**Agent attempts to connect from Mac to Pi app:**

```bash
# From Mac host:
curl -s http://<RASPBERRY_PI_IP>:8765/ | head -20
# Expected: HTML of dashboard landing page (or SSO login page)

# Or user can open in browser:
# http://raspberrypi.local:8765
# Expected: EVE Dashboard landing page loads
```

### Step 8: 24-Hour Soak Test (Agent Monitors)

**Agent sets up automated monitoring:**

```bash
# Create monitoring script on Pi
ssh pi@<IP> "cat > ~/monitor-eve-dashboard.sh << 'EOF'
#!/bin/bash
while true; do
  status=$(systemctl is-active eve-dashboard)
  timestamp=$(date '+%Y-%m-%d %H:%M:%S')
  echo "[$timestamp] Service status: $status"
  
  if [ "$status" != "active" ]; then
    echo "ERROR: Service not active! Checking logs..."
    journalctl -u eve-dashboard -n 20
    break
  fi
  
  # Check for OOM kills
  if dmesg | tail -5 | grep -q "eve-dashboard"; then
    echo "ERROR: OOM killer detected! Service may be out of memory."
    break
  fi
  
  sleep 300  # Check every 5 minutes
done
EOF
chmod +x ~/monitor-eve-dashboard.sh"

# Run monitor in background for 24 hours
ssh pi@<IP> "nohup ~/monitor-eve-dashboard.sh > ~/eve-dashboard-monitor.log 2>&1 &"
```

**After 24 hours, agent checks:**

```bash
ssh pi@<IP> "cat ~/eve-dashboard-monitor.log"
# Expected: 24+ "Service status: active" entries, no errors
```

### Step 9: Document Findings (Agent Creates Summary)

**Agent creates markdown report with:**
- Deployment duration (how long setup took)
- CPU/memory usage during build and runtime
- Any warnings or issues encountered
- Configuration that worked
- Recommended tuning for Pi model
- Performance metrics (startup time, response time)
- Backup strategy for data/logs

---

## Acceptance Criteria

All of these must be true before marking task complete:

- [ ] Pre-deployment checklist passed (disk, memory, network, time sync)
- [ ] Repository cloned or synced on Pi
- [ ] `bash setup-raspberrypi.sh` completed successfully
- [ ] Docker image built for ARM architecture without errors
- [ ] Systemd service installed and enabled
- [ ] Container running and health checks passing
- [ ] Application accessible at http://raspberrypi.local:8765
- [ ] Credentials loaded and no auth errors in logs
- [ ] 24-hour soak test completed with zero crashes
- [ ] Service auto-restarts successfully after systemctl restart
- [ ] Service auto-starts successfully after Pi reboot
- [ ] Logs are readable and contain no exceptions
- [ ] Memory usage stable (<250MB for Pi 3, <500MB for Pi 4)
- [ ] CPU usage reasonable (not pegged at 100% continuously)
- [ ] Synthesis report saved with findings and tuning recommendations
- [ ] Git commit with deployment documentation (if any README updates)

---

## Architecture Gotchas & Constraints

### 1. ARM Architecture Compilation
- Docker image builds from source on Pi (Python binary compilation)
- **First build**: 10-15 min (Pi 3 ~15 min, Pi 4 ~8 min, Pi 5 ~3 min)
- **Subsequent builds**: <1 min (layers cached)
- **Constraint**: Be patient during first build; don't power-cycle Pi

### 2. SD Card I/O Bottleneck
- SQLite database writes can be slow on cheap SD cards
- **Symptom**: Sync job takes longer than expected, high disk I/O
- **Solution**: Use Class 10 or UHS-III SD card (recommended for all Pi work)
- **Alternative**: Extern USB SSD if you have one

### 3. Memory Pressure
- Pi 3 with 1GB RAM, Docker container at 512MB limit, OS at ~300MB = tight
- **Symptom**: OOM killer terminates container during large sync
- **Solution**: Reduce docker-compose.yml memory limit to 256MB for Pi 3
- **Monitor**: Check `free -h` during runtime

### 4. CPU Throttling
- Pi 3 CPU throttles if temp exceeds 80°C
- **Symptom**: Build/runtime slower than expected, fluctuating performance
- **Solution**: Ensure adequate airflow; consider heatsink
- **Monitor**: Check `vcgencmd measure_temp` or `cat /sys/class/thermal/thermal_zone0/temp`

### 5. Network Stability
- WiFi on Pi can be flaky, drops packets during upload/download
- **Symptom**: Partial file transfers, sync retries
- **Solution**: Use Ethernet (via USB adapter if no built-in port)
- **Monitor**: `ping -c 100 8.8.8.8` and check packet loss

### 6. Time Synchronization
- If Pi time is >2 hours off, Docker TLS cert validation fails
- **Symptom**: "certificate verify failed" errors during build
- **Solution**: Enable NTP: `sudo timedatectl set-ntp true`
- **Verify**: `timedatectl status` should show "System clock synchronized: yes"

### 7. Backup Strategy
- `data/` directory contains SQLite database and logs
- **Never delete** data/eve_dashboard.db without backup
- **Recommended**: Daily backup to external storage or cloud
- **Script provided**: In README.raspberrypi.md (backup section)

---

## Dependencies & Blockers

### Prerequisites (All Met)
- ✅ eve-dashboard repo cloned on Mac
- ✅ setup-raspberrypi.sh script exists and is executable
- ✅ eve-dashboard.service file exists
- ✅ Docker image builds on x86_64 (verified in previous task)
- ✅ All tests pass on Mac

### Blocked By
- **Hardware**: Requires Raspberry Pi to be available
- **Network**: Requires Pi to be accessible from Mac (SSH)
- **User Input**: Requires credentials loaded by user

### Blocks
- None — this is final deployment task

---

## Local Worker Triage Report

**Template Conformance**: PASS — All sections complete  
**Hardware Requirements**: CLEAR — Specific Pi models listed, resource constraints documented  
**Automation Level**: MEDIUM — Partially automated (setup script), partially manual (user responsibility)  
**Risk Assessment**: LOW-MEDIUM — Well-tested deployment path, gotchas documented  
**MVP Alignment**: VALID — Deployment is key feature of project (Pi support is design goal)  
**Estimated Effort**: 2-3 hours (including 24-hour soak test)  

**Recommendation**: Ready for dispatch AFTER user has Raspberry Pi hardware and wants 24/7 deployment. Currently BACKLOG (low priority) because it requires hardware not mentioned as immediate need.

---

## Estimated Effort

- **Setup & verification**: 30 min
- **Docker build on Pi**: 10-15 min (first time only)
- **Credentials configuration**: 5 min
- **Functional testing**: 15 min
- **24-hour soak test**: 1440 min (= 24 hours; agent monitors, not actively working)
- **Documentation & synthesis**: 30 min
- **Total active time**: ~1.5 hours + 24 hours monitoring

---

## Notes for Next Agent

- If Pi hardware unavailable, task should remain in BACKLOG
- If user later gets Pi and wants deployment, move this task to ACTIVE
- Synthesis report should include CPU model (`lscpu`), OS version, SD card model, and actual build/runtime times
- Test results should be archived in `summaries/` folder with timestamp

---

## Questions for User Before Dispatch

- [ ] Do you have Raspberry Pi hardware available?
- [ ] Which Pi model? (3, 4, or 5?)
- [ ] Is it currently running? Can you SSH to it?
- [ ] Do you want 24/7 deployment, or just want to test?
- [ ] Should we set up automated backups of the data directory?

**Only proceed with this task after user answers "yes" to questions 1-3.**
