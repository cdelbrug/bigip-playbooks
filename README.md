# bigip-playbooks

Some generic playbook roles for creating a new VIP or adding sites to an existing VIP with related GTM config.

- `playbooks/new-vip.yml` - Create your new VIPs using LTM policy to forward based on host header to pools
- `playbooks/new-site.yml` - Create and add pools to existing VIP LTM policy and create related GTM configuration

## Requirements

- Ansible
- The [`f5networks.f5_modules`](https://docs.ansible.com/projects/ansible/latest/collections/f5networks/f5_modules/index.html) collection:
  ```bash
  ansible-galaxy collection install f5networks.f5_modules
  ```

## Layout

- `inventory/hosts.yml` - BIG-IP devices, grouped under `f5`
- `roles/f5/tasks/` - reusable LTM/GTM task files (monitor, pool, policy, VIP, GTM pool/monitor/wideip), included from the playbooks via `include_role`
- `roles/f5/defaults/main.yml` - shared defaults (GTM device names, LTM profile names)
- `playbooks/` - entry-point playbooks that loop over a `site_list` and call into the `f5` role

## Usage

Credentials aren't stored in the repo. Pass them as extra vars at runtime:

```bash
ansible-playbook playbooks/new-vip.yml -e username=myuser -e password=mypassword
```

Alternatively, uncomment the `environment:` block at the top of a playbook and export `F5_USER`, `F5_PASSWORD`, and `F5_VALIDATE_CERTS` instead of passing `-e` flags each time.

Each playbook defines its site data inline under `vars.site_list` (or `vars.vip` for `new-vip.yml`) — edit that block with your domain, pool members, and VIP details before running, then:

```bash
ansible-playbook playbooks/new-site.yml -e username=myuser -e password=mypassword
```
