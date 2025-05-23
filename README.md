Local kcp Management Script for Gardener Dashboard (`gardenerless-setup.sh`)

## Introduction

This repository provides a Bash script (`gardenerless-setup.sh`) to simplify the setup and management of a local **kcp** installation for the Gardener Dashboard. It automates workspace and project management, CRD/application of essential resources, and demo environment creation—enabling you to get started quickly with a dedicated, self-contained kcp setup.

> **Note:** This script works with a dedicated kcp directory (`kcp/`) and uses hardcoded kubeconfig paths. All network alias handling (for macOS) is built in—no manual aliasing is required.

---

## Features

* **kcp Repository Management**
  Clones or updates `github.com/kcp-dev/kcp` under `./kcp`, builds the binary, and keeps it current.

* **Dynamic KUBECONFIG Handling**
  Generates temporary kubeconfigs based on `kcp/.kcp/admin.kubeconfig` and transparently switches contexts and workspaces.

* **Workspace & Context Navigation**
  `--workspace <path>` lets you enter any top-level or nested workspace under `root:`.

* **Local CRD & Resource Bootstrap**
  Applies Gardener CRDs and seed/cloudprofile YAMLs from `resources/`—no external downloads.

* **Project & Shoot Commands**

  * `add-project` / `add-projects`
  * `add-shoot`   / `add-shoots`
    Projects and shoots can be created individually or in bulk, with status automatically set to **Ready** or **Error** based on naming.

* **Demo Environments**

  * `create-demo-workspaces` (animals, plants, cars)
  * `create-single-demo-workspace`
    All CRDs, AWS secrets, and sample shoots/projects are preconfigured.

* **Token Management**
  `get-token` creates/refreshes a 24h dashboard-user token in the `garden` namespace.

* **Reset Utilities**

  * `reset-kcp`        clears all local state
  * `reset-kcp-certs`  removes certs/keys

* **macOS Compatibility**
  Automatically configures the `lo0` alias (`192.168.65.1/24`) if needed.

---

## Prerequisites

* **Git**
* **Go** (for building kcp)
* **kubectl**
* **yq** (CLI YAML processor)

---

## Quickstart

1. **Clone & enter repo**

   ```bash
   git clone <repo-url>
   cd <repo-dir>
   ```

2. **Build kcp**

   ```bash
   ./gardenerless-setup.sh setup-kcp
   ```

3. **Start kcp server**

   ```bash
   ./gardenerless-setup.sh start-kcp
   ```

   > macOS users: loopback alias is added automatically.

4. **(Optional) Reset state**

   ```bash
   ./gardenerless-setup.sh reset-kcp
   ```

5. **Create a single demo workspace**

   ```bash
   ./gardenerless-setup.sh create-single-demo-workspace
   ```

   → Kubeconfig: `kcp/.kcp/dashboard.kubeconfig`

6. **Create full demo suite for kcp mode**

   ```bash
   ./gardenerless-setup.sh create-demo-workspaces
   ```

   → Kubeconfig: `kcp/.kcp/dashboard-kcp.kubeconfig`

---

## Usage

```bash
./gardenerless-setup.sh [--workspace <ws>] <command> [options]
```

* **Global flag**
  `--workspace <ws>`
  Enter any workspace path under `root` (e.g. `foo`, or nested `root:foo:bar`).

### Commands & Options

| Command                          | Options                                                                      | Description                                                   |                                           |
| -------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------- | ----------------------------------------- |
| **setup-kcp**                    | —                                                                            | Clone & build the kcp binary                                  |                                           |
| **start-kcp**                    | —                                                                            | Start the kcp server (foreground, macOS alias)                |                                           |
| **reset-kcp**                    | —                                                                            | Delete all `kcp/.kcp` state                                   |                                           |
| **reset-kcp-certs**              | —                                                                            | Remove certs/keys in `kcp/.kcp`                               |                                           |
| **setup-gardener-crds**          | —                                                                            | Apply CRDs from `resources/crds/`                             |                                           |
| **cluster-resources**            | —                                                                            | Apply `cloudprofile-*.yaml` & `seed-*.yaml` from `resources/` |                                           |
| **get-token**                    | —                                                                            | Create/refresh 24h token for `dashboard-user` SA              |                                           |
| **dashboard-kubeconfigs**        | —                                                                            | Print paths of generated dashboard kubeconfigs                |                                           |
| **add-project**                  | `--name <name>`<br>`[--namespace <ns>]`                                      | Create one project (status=Ready)                             |                                           |
| **add-projects**                 | `--count <n>`                                                                | Bulk-create *n* projects (random UIDs, status=Ready)          |                                           |
| **add-shoot**                    | `--shoot <name>`<br>`--project <proj>`                                       | Create one shoot; suffix `-error` ⇒ Error state               |                                           |
| **add-shoots**                   | `--project <proj>`<br>`--count <n>`                                          | Bulk-create *n* shoots (healthy by default)                   |                                           |
| **toggle-shoot-status**          | `--shoot <name>`<br>`--project <proj>`<br>\`--mode \<ready                   | error>\`                                                      | Flip a shoot’s status between Ready/Error |
| **random-update-shoots**         | `[--project <proj>]`<br>`--interval <seconds>`                               | Periodically toggle one shoot every *interval* seconds        |                                           |
| **simulate-shoot-op**            | `--shoot <name>`<br>`--project <proj>`<br>`--interval <s>`<br>`[--step <%>]` | Simulate progress 0→100% on a shoot                           |                                           |
| **create-demo-workspaces**       | —                                                                            | Build `demo-animals`, `demo-plants`, `demo-cars` with samples |                                           |
| **create-single-demo-workspace** | —                                                                            | Build one `demo` workspace with samples                       |                                           |

---

## Examples

```bash
# Build kcp
./gardenerless-setup.sh setup-kcp

# Start server
./gardenerless-setup.sh start-kcp

# Apply CRDs & resources in demo-animals
./gardenerless-setup.sh --workspace demo-animals setup-gardener-crds
./gardenerless-setup.sh --workspace demo-animals cluster-resources

# Create one project named "foo"
./gardenerless-setup.sh add-project --name foo

# Bulk-create 5 projects
./gardenerless-setup.sh add-projects --count 5

# Add a shoot "bar" to project "foo"
./gardenerless-setup.sh add-shoot --shoot bar --project foo

# Bulk-create 10 shoots in "foo"
./gardenerless-setup.sh add-shoots --project foo --count 10
```

---

## Troubleshooting

* **kcp binary missing?**
  Run `./gardenerless-setup.sh setup-kcp`.

* **Server not running?**
  Run `./gardenerless-setup.sh start-kcp`.

* **Missing admin kubeconfig?**
  Verify `kcp/.kcp/admin.kubeconfig` exists.

* **Permission issues?**
  Check Git, Go, and `kubectl` credentials and access.

If problems persist, inspect script logs and ensure all prerequisites are met.

---

Happy clustering!
