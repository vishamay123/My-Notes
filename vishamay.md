# 1. Are both containers running?
docker compose ps

# Expected output:
# NAME                 STATUS          PORTS
# ocsinventory         Up X minutes    0.0.0.0:80->80/tcp
# ocsinventory-db      Up X minutes    3306/tcp

# 2. Check container health
docker inspect ocsinventory-db | grep -A5 '"Health"'

# 3. Test web UI is reachable from server itself
curl -s -o /dev/null -w "%{http_code}" http://localhost/ocsreports/
# Should return: 200 or 302

# 4. Test agent endpoint
curl -s -o /dev/null -w "%{http_code}" http://localhost/ocsinventory
# Should return: 400 (expected — agents need to POST valid XML)
# A 400 here is GOOD. It means the endpoint is alive and rejecting empty requests.

# 5. Check logs for errors
docker compose logs --tail=50 ocsinventory
docker compose logs --tail=50 ocsinventory-db
```

---

## SECTION 5 — OCS INVENTORY NG INITIAL CONFIGURATION

### 5.1 First Login

1. Open browser: `http://[server-ip]/ocsreports`
2. Default credentials:
   - Username: `admin`
   - Password: `admin`
3. **Immediately change the password** after login:
   - Top right → `admin` → **Change password**
   - Set a strong password and save it in your password manager

### 5.2 Admin Panel Navigation

After login, the top navigation shows:
- **All computers** — Your main dashboard
- **Inventory** — Hardware/software search and filters
- **Deployment** — Push packages to agents (Phase 3 capability)
- **Networks** — Network scan results
- **Configuration** — All server-side settings (what we configure now)
- **Reports** — Saved reports and export

### 5.3 Critical Configuration Settings

Go to **Configuration → General Configuration**

#### Agent Communication Settings

| Setting | Value | Why |
|---|---|---|
| FREQUENCY | 24 | Agents report every 24 hours. Sufficient for 100 machines and low server load. |
| PROLOG_FREQ | 24 | How often agent sends a prolog (handshake) to check for new instructions |
| INVENTORY_DIFF | 1 | Only send **changed** inventory data, not full inventory every time. Critical for efficiency with 100 machines. |
| AUTO_DUPLICATE_LVL | 15 | Threshold for flagging potential duplicate machine entries |
| LOGLEVEL | 1 | Server-side log verbosity. Use 1 (verbose) during setup, change to 0 later |

#### Software Inventory Settings

| Setting | Value | Why |
|---|---|---|
| TRACE_DELETED | 1 | Track when software is removed — critical for change detection |
| REGISTRY | 1 | Collect Windows registry software entries in addition to Add/Remove Programs |
| SNMP | 0 | Disable SNMP scan for now (not needed for Phase 1) |

#### Lock and Security Settings

| Setting | Value | Why |
|---|---|---|
| LOCK_REUSE_TIME | 600 | Prevents the same machine from submitting multiple inventories within 600 seconds (duplicate protection) |

### 5.4 Creating Tags for Grouping

OCS supports **tags (administrative data)** to group machines. This is useful for:
- Department grouping (IT, Finance, HR)
- Location grouping (Floor 1, Floor 2)
- Machine type (Laptop, Desktop, Server)

Go to **Configuration → Administrative Data**:
- Click **Add**
- Create fields like: `Department`, `Location`, `Asset Tag`, `Owner`
- Type: `Text` or `Dropdown` as appropriate

These fields appear in each machine's record and you can filter/search by them.

### 5.5 Recommended Settings for 100 Machines
```
General:
  FREQUENCY = 24
  PROLOG_FREQ = 24
  INVENTORY_DIFF = 1 (saves bandwidth and server load)

Inventory:
  TRACE_DELETED = 1 (critical — tracks removed software)
  REGISTRY = 1 (full Windows software list)

Interface:
  GUI_SHOW_IP = 1 (show IP in the all-computers list)
  GUI_SHOW_OS = 1
  GUI_SHOW_OS_VERSION = 1
```

### 5.6 Network Discovery (Optional for Phase 1)

OCS has an IP scan / network discovery feature (IPDISCOVER) that uses agents as network probes to detect unmanaged devices. For Phase 1:
- Leave this **disabled**
- Enable in Phase 2 when all agents are deployed and stable
- Go to: **Configuration → IpDiscover** → set `IPDISCOVER = 0`

---

## SECTION 6 — AGENT DEPLOYMENT PLAN

### 6.1 Strategy Overview
```
PHASE 1 — PILOT (Week 1)
  └── Deploy to 5 machines manually
      ├── 2 Windows machines
      ├── 2 Linux machines
      └── 1 test machine (ideally your own)
  └── Verify all 5 appear correctly in dashboard
  └── Confirm software inventory is accurate
  └── Fix any issues

PHASE 1 — FULL ROLLOUT (Week 2–3)
  └── Windows: Deploy via GPO or manual/batch script
  └── Linux: Deploy via Ansible or manual package install
  └── Target: 100/100 machines reporting within 48 hours
