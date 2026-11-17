# RISE Build Farm & Developer Tooling WG — 2026 Bi-Weekly Progress Log

> **Living Snapshot:** See [`current_status.md`](current_status.md) for the current inventory of all 19 Hetzner `CCX63` servers, `riscv-runner` pools, active CI workloads, and QEMU enablement items.
> **Hardware Testbed (OSU-OSL) Weekly Log:** See [`docs/developer-cloud/2026_weekly_progress.md`](../developer-cloud/2026_weekly_progress.md) for the weekly OSU-OSL Developer Cloud (Board Farm) hardware and Labgrid logs.
> **Ordering:** Reverse chronological (newest meeting at the top). Covers the bi-weekly Thursday **RISE Developer Tooling WG** calls, **RISE TSC** deep dives, and **RISE Governing Board** infrastructure reviews.
>
> **Internal details:** Addresses, budgets, contracts and meeting recordings are not published here. RISE members can find them in the RISE shared drive or ask the WG maintainers. Build Farm host details are in `RISE Build Farm Guide for Administrators (Hetzner Instances)`.

---

## 2026-11-12 — RISE Governing Board Deep Dive & Infrastructure Decisions (Nov 11–13)

### 1. Developer Tooling Deep Dive & 2027 Budget Approvals
* **Governing Board Deep Dive:** Puneetha Ramachandra, Hai Bi, and Daniel Barboza presented the Developer Tooling WG Deep Dive covering **Developer Infrastructure (Build Farm & Developer Cloud)** and **Simulator / Emulator (QEMU)**.
* **Native GitHub-Hosted Larger Runners Approved:**
  * Approved reallocating funds from discontinued custom-managed x86 cloud runners to native **GitHub-Hosted Larger Runners** (`32 vCPU / 128 GB RAM` class) for bursty x86 compilation workflows (`<40%` duty cycle), eliminating OS/runner maintenance overhead while keeping the budget neutral *(details in a separate Google Doc)*.
* **Cross-Distro Developer Images RFP Presented:**
  * Presented the 12-month **Cross-Distro Stages** RFP (co-TL'd by Puneetha Ramachandra, Nathan Egge, and Brian Harrington) to productize automated, continuously tested bootable RISC-V images across **Ubuntu, Red Hat, Debian, and Gentoo** paired with board-specific U-Boot and OpenSBI for the RISC-V Developer Cloud and GitHub Actions runners. No objections were raised and no vote was taken; the RFP goes to the TSC next *(details in a separate Google Doc)*.
* **Upstream Workload Onboarding Priority Confirmed:**
  * Confirmed the initial upstream CI onboarding sequence across the Build Farm + OSU-OSL Developer Cloud as **Yocto CI → Android 17 (AOSP) CI → Linux Kernel CI**, followed by broader upstream project onboarding.

### 2. Build Farm, Yocto Builder & Documentation Updates
* **Dedicated Yocto x86 Builder (`rise-python-ci-1`):**
  * Initiated reprovisioning of `rise-python-ci-1` (`48 vCPU`, `192 GB RAM`, `960 GB NVMe`) as a dedicated Yocto `meta-riscv` x86 builder with persistent `sstate-cache` and `downloads` directories (cutting `meta-riscv` build times from ~2–7 hours to minutes), paired with automated physical TFTP netboot testing on OSU-OSL's `a210-3` (`pool=a210-yocto`).
* **LLVM / Compiler Fuzzer Audit Follow-Up (`rise-compiler-fuzzer-ci-1..2`):**
  * Confirmed on the WG mailing list (`2026-11-11`) that the only LLVM-assigned machines on Hetzner are `rise-compiler-fuzzer-ci-1` and `rise-compiler-fuzzer-ci-2` (registered to `patrick-rivos/compiler-fuzz-ci`), both currently at `0.00` load (`ci-1` idle since `2026-07-13` due to GitHub's 60-day workflow inactivity auto-disable; `ci-2` idle since a `2026-03-01` reboot without `systemd`). Ready to re-point to `riseproject-dev` under `systemd` or repurpose for Toolchain WG / LLVM CI needs.
* **Public GitHub Status & Progress Tracking:**
  * Established version-controlled `current_status.md` and `2026_weekly_progress.md` tracking across both [`riseproject-dev/developer-cloud`](https://github.com/riseproject-dev/developer-cloud) and [`riseproject-dev/developer-infrastructure-wg`](https://github.com/riseproject-dev/developer-infrastructure-wg), with internal network details and budget/contract details kept out of the public pages.

---

## 2026-11-05 — Bi-Weekly Developer Tooling WG & TSC (AM) Yocto Deep Dive (Nov 5–6)

### 1. Hetzner 19-Server Fleet Audit (`19 × CCX63`) & Reallocation Decisions
* **Fleet-Wide Audit Completed (Oct 27 – Nov 5):** Audited all **19 Hetzner `CCX63` servers** (`48 vCPU`, `192 GB RAM`, `960 GB NVMe` in `hel1-dc2`) across CPU load, active systemd services, and GitHub runner registrations *(server IPs and IDs recorded in `RISE Build Farm Guide for Administrators (Hetzner Instances)`)*:
  1. **Grandfathered Pricing Rule:** Confirmed that all 19 `CCX63` servers carry a grandfathered monthly rate well below Hetzner's current retail rate for newly created or resized `CCX63` instances. **Decision:** Never delete and recreate an idle `CCX63` server; always reconfigure and repurpose servers in-place *(details in a separate Google Doc)*.
  2. **`rise-python-ci-1` (`1` host) → Repurposed to Yocto x86 CI Builder:** Because Python wheel builds now run on `riscv-runner` and GitHub Actions, `rise-python-ci-1` was approved for in-place reprovisioning as the dedicated x86 Yocto builder for Valentina Fernandez and Trevor Gamblin (BayLibre).
  3. **`rise-gcc-ci-05` .. `rise-gcc-ci-11` (`7` hosts, 14 runners) — GCC Post-Commit CI:** Confirmed 6 of 7 hosts (`05`, `06`, `08`–`11`) are actively running daily 4–8+ hour multilib GCC post-commit builds, while `07` was idle after a reboot without `systemd`. **Action:** Jiawei (ISCAS) is leading the migration pilot starting on `rise-gcc-ci-07` to re-register all runners to [`riseproject-dev/gcc-postcommit-ci`](https://github.com/riseproject-dev/gcc-postcommit-ci) under `systemd` and consolidate runners where possible.
  4. **`rise-gcc-ci-01` .. `rise-gcc-ci-04` (`4` hosts, 9 runners) — GCC Pre-Commit CI:** Root-caused why the 8 build runners on `01`–`04` had been idle since `2026-07-09`: `precommit-ping` on `01` was polling Patchwork every 15 minutes on `ewlu/gcc-precommit-ci` and failing at `git checkout` due to a broken `newlib` submodule URL (already fixed in [`riseproject-dev/gcc-precommit-ci`](https://github.com/riseproject-dev/gcc-precommit-ci)). **Action:** Re-registering `01`–`04` to `riseproject-dev/gcc-precommit-ci` with container isolation.
  5. **`rise-compiler-fuzzer-ci-1` .. `2` (`2` hosts) — Compiler Fuzzers:** Identified both hosts sitting idle on `patrick-rivos/compiler-fuzz-ci`; flagged for Toolchains WG confirmation to re-point to `riseproject-dev` or repurpose for LLVM CI.
  6. **`rise-linux-kernel-ci-1` .. `5` (`5` hosts) — Linux Kernel CI:** Confirmed active on RISC-V Linux Patchwork pre-commit and post-commit CI (Björn Töpel).

### 2. Yocto Project Deep Dive (`meta-riscv` & `AutoBuilder` Compute Needs)
* Presented by Valentina Fernandez (BayLibre) at the `2026-11-05` TSC (AM) meeting:
  * **`meta-riscv` Mainline Policy & Profile Tunes:** `meta-riscv` is officially mirrored on `git.yoctoproject.org`, deprecated legacy vendor-kernel boards (`VisionFive`, `Star64`, `Nezha D1`) in favor of mainline kernels (`Zhihe A210` on `7.2.12`, `SpacemiT K3 Pico-ITX` on `7.3.1`, `SiFive Premier P550`), merged `riscv64gc`/`riscv64gcv` tunes into `openembedded-core`, and opened a draft PR for standardized `RVA20`, `RVA22`, and `RVA23` tuning profiles.
  * **Two Distinct Compute Requirements Scoped:**
    1. **`meta-riscv` BSP CI:** Dedicated x86 builder (`rise-python-ci-1`) + OSU-OSL Developer Cloud physical boot testing (`A210` and `K3`), with Yocto's **`resulttool`** patched to export Board Farm test results back to the upstream Yocto `AutoBuilder`.
    2. **Official Yocto Project `AutoBuilder` (`openembedded-core`):** Needs a **dedicated RVA23 server with KVM acceleration** to replace slow TCG software-emulated `qemuriscv64` `ptest` runs (benchmarking KVM `ptest` speedups on a `SpacemiT K3 Pico-ITX`).

### 3. Secret Management Architecture (`2026-11-06` Mailing List Thread)
* Aligned on moving RISC-V CI and GitHub Actions runner credentials out of single-admin personal 1Password storage into a shared multi-admin secret store, evaluating **Scaleway Secret Manager** first alongside tokenless OIDC Workload Identity Federation options.

---

## 2026-10-22 — Bi-Weekly Developer Tooling WG & TSC (AM) Sync

### 1. Dev Tooling Repository Watch (`riscv-runner`, Python/AI & Toolchain CI)
* **`riseproject-dev/riscv-runner` (`2026-10-21`):** Added **openKylin `UR DP-1000`** hardware detection and `conda-forge` allocation, enabled SpacemiT `K1` access for `NumPy`, implemented cross-entity quota balancing, and published multi-arch Kubernetes runner images.
* **Python & AI Ecosystem:**
  * **[`riseproject-dev/python-wheels`](https://github.com/riseproject-dev/python-wheels):** Continuous daily `riscv64` binary wheel builds and new package additions (`torchrl`, `dprint-py`, `pyxdia`, `transformations`, etc.) plus CI branch optimizations.
  * **[`riseproject-dev/python-versions`](https://github.com/riseproject-dev/python-versions) (`2026-10-12`):** Containerized package builds, `PREFIX` fixes, and automated manifest updates (`#25`–`#27`).
  * **[`riseproject-dev/sw-ecosystem`](https://github.com/riseproject-dev/sw-ecosystem) (`2026-10-18`):** Added `tinygrad` readiness report and rewrote `V8`, `Valgrind`, `SwiftShader`, `tcmalloc`, `tokenizers`, and `Traefik` ecosystem readiness reports.
* **Toolchain CI Migration to `riseproject-dev`:**
  * **[`riseproject-dev/gcc-postcommit-ci`](https://github.com/riseproject-dev/gcc-postcommit-ci):** Migrated repository ownership to RISE with automated status updates to [`riseproject-dev.github.io/gcc-postcommit-ci/`](https://riseproject-dev.github.io/gcc-postcommit-ci/) (maintained by Jiawei).
  * **[`riseproject-dev/gcc-precommit-ci`](https://github.com/riseproject-dev/gcc-precommit-ci) & [`riscv-gnu-toolchain-ci`](https://github.com/riseproject-dev/riscv-gnu-toolchain-ci) (`2026-10-08`):** Switched to mirrored toolchain sources with submodule checkout retries (`#2`), restoring Patchwork polling and PR validation. Noted follow-up item to detach `gcc-postcommit-ci` and `riscv-gnu-toolchain-ci` from upstream personal fork parent links (`gcc-precommit-ci` is already standalone).

### 2. Simulator / Emulator (QEMU) Status Review
* Reviewed active QEMU mailing list series:
  * **Completed (`SE_01_022`):** **RISC-V Server SoC Reference Board** merged and scheduled for release in **QEMU 11.2**.
  * **In Review:** `SE_01_003` (WorldGuard support, split into smaller series), `SE_01_004` (IOPMP v11), `SE_01_005` (PCIe passthrough on x86 hosts v1), `SE_01_018` (ACPI PPTT support v1), and `SE_01_029` (P Extension support v1).

### 3. Governance & Information Firewall Policy
* Reconfirmed at the `2026-10-22` TSC (AM) meeting that budgets, RFPs/contracts in flight, and contractor evaluations remain internal to RISE members while technical infrastructure status and upstream CI progress are shared publicly *(details in a separate Google Doc)*.

---

## 2026-10-08 — Bi-Weekly Developer Tooling WG

### 1. Working Group Consolidation & Toolchain / Runner Updates
* **Working Group Consolidation:** Completed the consolidation of the **Developer Infrastructure WG** and **Simulator / Emulator WG** into the unified **RISE Developer Tooling Working Group** (co-chaired by Daniel Barboza, Puneetha Ramachandra, and Hai Bi; mailing list: [`developer-tooling-wg@lists.riseproject.dev`](https://lists.riseproject.dev/g/developer-tooling-wg)).
* **Repository & Runner Updates:**
  * **[`riseproject-dev/riscv-runner`](https://github.com/riseproject-dev/riscv-runner):** Added Alibaba Zhihe A210 and SpacemiT K3 device detection in the K8s device plugin, updated Ansible runner setup and Scaleway lifecycle playbooks, and added `ubuntu-26.04-riscv` job routing.
  * **[`riseproject-dev/riscv-runner-sample`](https://github.com/riseproject-dev/riscv-runner-sample):** Verified `actions/setup-python` integration with the RISE Python wheel mirror.
  * **[`riseproject-dev/pypi-proxy`](https://github.com/riseproject-dev/pypi-proxy):** Redirected `/simple` directly to `python-wheels` GitHub Pages and archived the standalone proxy repository.
  * **Toolchain CI Review:** Coordinated with Kito Cheng (SiFive), Craig Topper (SiFive), and Jeff Law (Qualcomm) on reviewing the `riseproject-dev/gcc-postcommit-ci` migration PRs.

### 2. Cross-Distro Stages Proposal & All Things Open (ATO) Demo Prep
* **Cross-Distro Stages Proposal:** Initiated the proposal to expand the Gentoo stage builder workstream into a cross-distro automated bootable image pipeline (Ubuntu, Red Hat, Debian, Gentoo) for the Board Farm and Build Farm *(details in a separate Google Doc)*.
* **All Things Open (ATO) Conference Demo Prep (Oct 19–20):** Prepared the interactive booth demo showcasing the public Board Farm dashboard, RISC-V GitHub Actions runner onboarding, and compiler CI dashboards.

---

## 2026-09-24 — Bi-Weekly Developer Tooling WG & TSC (AM) Yocto Deep Dive

### 1. Mandatory Source Mirroring in `riseproject-dev` & Runner Storage Policy
* **Mandatory Source Mirroring (`riseproject-dev`):**
  * During the review of the 3-Stage Hardware Onboarding Policy (`board-farm#2`), established a mandatory rule that all kernel, U-Boot, OpenSBI, and CI toolchain sources must be mirrored into **RISE-owned GitHub repositories (`riseproject-dev`)** so CI pipelines and board support packages are never broken if an upstream personal or vendor repository is deleted or archived (such as when the original T-Head `TH1520` Gitee repository was removed).
* **Tiered Storage Strategy for `riscv-runner` Nodes:**
  * Because the `riscv-runner` Ubuntu container image (~4 GB compressed, ~30 GB uncompressed) requires substantial local scratch space, agreed on a tiered storage strategy pairing 128 GB SSDs (matching Scaleway `EM-RV1` bare-metal nodes) with the subset of Developer Cloud boards running Kubernetes `riscv-runner` pods.

### 2. Yocto Year 2 Deep Dive & 30–40% QEMU `qemuriscv64` `ptest` Speedup
* **Yocto `AutoBuilder` & `resulttool` Integration (Valentina Fernandez):**
  * Following the RISE–Yocto Platinum Member sync, Yocto agreed to build two `meta-riscv` BSPs (`SpacemiT K3 Pico-ITX` and `Milk-V Duo`) on the official Yocto Project `AutoBuilder`, mirror `meta-riscv` on `git.yoctoproject.org`, and integrate RISE hardware test results via **`resulttool`**.
* **30–40% QEMU `qemuriscv64` `ptest` Speedup (Trevor Gamblin & Daniel Barboza):**
  * Upstreamed a QEMU RISC-V TLB-flush optimization in TCG mode that reduced `qemuriscv64` `ptest` execution times across the build farm by **30–40%** (`coreutils` `ptest` runtime improved by **38%**).
