# RISE Build Farm

## Living Status & Progress Tracking

* **[Current Status & 19-Server Hetzner Inventory (`current_status.md`)](current_status.md)** — Authoritative inventory of all 19 `CCX63` servers, active CI runners, `riscv-runner` Kubernetes fleet, and supporting infrastructure.
* **[2026 Bi-Weekly Progress Log (`2026_weekly_progress.md`)](2026_weekly_progress.md)** — Reverse-chronological log of Build Farm, `riscv-runner`, Toolchain CI, Python/AI CI, Yocto CI, and QEMU updates from the bi-weekly Developer Tooling WG, TSC, and Governing Board meetings.

## About

The RISE Developer Tooling Working Group (formerly Developer Infrastructure WG) maintains a build and simulation test farm for key open-source software components that run on RISC-V, running across Hetzner Cloud (`19 × CCX63` dedicated vCPU servers), Scaleway (`EM-RV1` native RISC-V fleet), GCP Spot instances, and GitHub-Hosted Larger Runners.

## Active Projects

* [RISE Linux Kernel CI](linux-kernel-ci.md) (Björn Töpel — [`linux-riscv`](https://github.com/linux-riscv))
* [RISE GCC Pre-Commit CI](gcc-precommit-ci.md) (Jiawei, Kito Cheng, Patrick O'Neill, Edwin Lu — [`riseproject-dev/gcc-precommit-ci`](https://github.com/riseproject-dev/gcc-precommit-ci))
* [RISE GCC Post-Commit CI](gcc-postcommit-ci.md) (Jiawei, Kito Cheng, Jeff Law, Patrick O'Neill, Edwin Lu — [`riseproject-dev/gcc-postcommit-ci`](https://github.com/riseproject-dev/gcc-postcommit-ci) & [Live Dashboard](https://riseproject-dev.github.io/gcc-postcommit-ci/))
* [RISE GCC Fuzz CI](gcc-fuzz-ci.md) (Min Hsu, Kito Cheng, Patrick O'Neill, Edwin Lu)
* [RISE LLVM Fuzz CI](llvm-fuzz-ci.md) (Min Hsu, Kito Cheng, Patrick O'Neill, Edwin Lu)
* [RISE Python RISC-V Wheel Builder](python-wheel-builder.md) (Mark Ryan, Ludovic Henry — [`riseproject-dev/python-wheels`](https://github.com/riseproject-dev/python-wheels))
* **Yocto `meta-riscv` Dedicated x86 Builder** (`rise-python-ci-1` — Valentina Fernandez, Trevor Gamblin, Puneetha Ramachandra, Hai Bi — paired with hardware boot testing on [`riseproject-dev/developer-cloud`](https://github.com/riseproject-dev/developer-cloud))
* **RISC-V Native GitHub Runner Fleet** (Ludovic Henry — [`riseproject-dev/riscv-runner`](https://github.com/riseproject-dev/riscv-runner))

## Past Projects

* [RISE OpenJDK CI](retired/openjdk-ci.md) (Ludovic Henry)
* [PoC Github Runners on GKE](retired/github-runners-gke.md)
* [RISE GLIBC CI](retired/glibc-ci.md) (Patrick O'Neill, Edwin Lu)
