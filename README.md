# Linux Asset Inventory Playbook

Thu thập thông tin asset inventory cho Linux systems sử dụng Ansible.

## 📁 Cấu trúc thư mục

```
ansible-asset-inventory/
├── inventory_report.yml      # Playbook đầy đủ
├── quick_inventory.yml       # Playbook nhanh
├── inventory/
│   └── hosts.yml             # Sample inventory
├── templates/
│   └── inventory_report.html.j2  # HTML template
└── README.md
```

## 🚀 Cách sử dụng

### Quick Inventory (Kiểm tra nhanh)

```bash
ansible-playbook -i inventory/hosts.yml quick_inventory.yml
```

Output:
- Console: Thông tin cơ bản từng host
- File: `/tmp/quick_inventory.csv`

### Full Inventory Report

```bash
ansible-playbook -i inventory/hosts.yml inventory_report.yml
```

Output:
- Console: Báo cáo chi tiết
- JSON: `/tmp/asset_inventory/<hostname>_inventory.json`
- CSV: `/tmp/asset_inventory/inventory_summary.csv`

### Chạy cho một host cụ thể

```bash
ansible-playbook -i inventory/hosts.yml inventory_report.yml --limit web01
```

### Chạy với verbose mode

```bash
ansible-playbook -i inventory/hosts.yml inventory_report.yml -v
```

## 📊 Thông tin thu thập

| Category | Details |
|----------|---------|
| **System** | Hostname, FQDN, OS, Kernel, Architecture, Serial, Virtualization |
| **Hardware** | CPU, Cores, vCPUs, Memory, Swap |
| **Storage** | Mount points, Size, Usage, Filesystem type |
| **Network** | IP, MAC, Gateway, DNS, Interfaces |
| **Software** | Installed packages, Running/Enabled services |
| **Users** | Local users, Sudo users, Last logins |
| **Security** | SELinux/AppArmor, Firewall, Open ports, SSH config |
| **Status** | Uptime, Load average, Reboot required |

## 📋 Output Formats

### CSV Summary (inventory_summary.csv)

```csv
Hostname,IP,OS,Version,vCPUs,Memory_MB,Packages,Running_Services,SELinux,Firewall,Uptime
web01,192.168.1.10,CentOS,8.4,4,8192,523,45,Enforcing,active,up 30 days
web02,192.168.1.11,Ubuntu,22.04,8,16384,892,62,N/A,active,up 15 days
```

### JSON Detail (hostname_inventory.json)

```json
{
  "collection_date": "2024-12-24T10:30:00Z",
  "system": {
    "hostname": "web01",
    "distribution": "CentOS",
    "kernel": "4.18.0-348.el8.x86_64"
  },
  "hardware": {
    "cpu": {...},
    "memory": {...},
    "disks": [...]
  },
  "security": {
    "selinux": "Enforcing",
    "firewalld": "active",
    "listening_ports": [...]
  }
}
```

## 🔧 Tùy chỉnh

### Thay đổi thư mục output

```yaml
vars:
  report_dir: "/path/to/your/reports"
```

### Thêm custom facts

```yaml
- name: Custom application check
  shell: /opt/myapp/version.sh
  register: myapp_version
  
- name: Add to inventory
  set_fact:
    custom_info:
      myapp_version: "{{ myapp_version.stdout }}"
```

## 🔒 Security Notes

- Playbook cần `become: yes` để truy cập một số thông tin
- Không thu thập passwords hoặc sensitive data
- Reports nên được lưu trữ an toàn
- Xem xét mã hóa JSON output nếu chứa thông tin nhạy cảm

## 📦 Requirements

- Ansible 2.9+
- Python 3.6+ trên target hosts
- SSH access với sudo privileges

## 🤝 Tích hợp

### Với AWX/AAP

1. Tạo Project từ Git repo chứa playbook
2. Tạo Inventory hoặc sync từ source
3. Tạo Job Template với playbook này
4. Schedule chạy định kỳ (hàng ngày/tuần)

### Với CMDB

Export JSON/CSV và import vào CMDB system:

```bash
# Chạy playbook
ansible-playbook -i inventory/hosts.yml inventory_report.yml

# Upload to CMDB API
curl -X POST https://cmdb.example.com/api/assets \
  -H "Content-Type: application/json" \
  -d @/tmp/asset_inventory/web01_inventory.json
```

## 📝 License

MIT License - Free to use and modify.
