# 🚀 Hướng dẫn triển khai Kubernetes Cluster + Jenkins với Ansible

## 📑 Table of Contents
1. [Cấu trúc source](#cấu-trúc-source)
2. [Chuẩn bị môi trường](#chuẩn-bị-môi-trường)
3. [Thực hiện triển khai](#thực-hiện-triển-khai)
   - [Chạy từng playbook riêng lẻ](#chạy-từng-playbook-riêng-lẻ)
   - [Chạy toàn bộ tự động với script](#chạy-toàn-bộ-tự-động-với-script)
4. [Kiểm tra kết quả](#kiểm-tra-kết-quả)
5. [Dọn dẹp (tuỳ chọn)](#dọn-dẹp-tuỳ-chọn)

---

## 📁 Cấu trúc source

```text
group_vars/
│   └── all.yml                # Biến toàn cục dùng cho mọi host
inventory/
│   └── hosts.yml              # Danh sách host (IP, nhóm node) để Ansible kết nối
playbooks/
│   ├── 01-prepare-nodes.yml   # Chuẩn bị môi trường các node (update, cài đặt docker,...)
│   ├── 02-setup-master.yml    # Cấu hình master Kubernetes
│   ├── 03-setup-workers.yml   # Cấu hình worker Kubernetes
│   └── 04-setup-jenkins.yml   # Cài đặt Jenkins server
roles/
│   ├── jenkins/tasks/
│   │   └── main.yml           # Các tasks triển khai Jenkins
│   ├── k8s-common/tasks/      # Tasks dùng chung cho Kubernetes cluster
│   ├── k8s-master/tasks/
│   │   └── main.yml           # Tasks riêng cho master node
│   └── k8s-worker/tasks/
│       └── main.yml           # Tasks riêng cho worker node
.gitignore                     # File ignore các file không cần commit
ansible.cfg                     # File cấu hình Ansible
cleanup.yml                     # Playbook xóa hạ tầng hoặc dọn dẹp node
deploy-*.sh                     # Script tự động chạy các playbook
DevOps-Key.pem                  # Key SSH để Ansible kết nối với các node
health.sh                       # Script kiểm tra tình trạng cluster / Jenkins
k8s-join-command                # File lưu lệnh join worker vào cluster
README.md                        # File hướng dẫn tổng quan
site.yml                         # Playbook tổng hợp (gọi tất cả playbook theo thứ tự)

🛠️ Chuẩn bị môi trường

Cài đặt Ansible trên máy control (máy bạn chạy playbook).

Đảm bảo có SSH key (DevOps-Key.pem) để kết nối đến các server (master, worker, Jenkins).

Kiểm tra file inventory/hosts.yml để chắc chắn các IP đúng.

Kiểm tra group_vars/all.yml để cấu hình chung (ví dụ: username, password, các biến môi trường).

⚡ Thực hiện triển khai
Chạy từng playbook riêng lẻ

ansible-playbook -i inventory/hosts.yml playbooks/01-prepare-nodes.yml
ansible-playbook -i inventory/hosts.yml playbooks/02-setup-master.yml
ansible-playbook -i inventory/hosts.yml playbooks/03-setup-workers.yml
ansible-playbook -i inventory/hosts.yml playbooks/04-setup-jenkins.yml

Chạy toàn bộ tự động với script
chmod +x deploy.sh
./deploy.sh
*** Lưu ý: khi chạy lệnh "./deploy.sh" phải tạo thêm terminal để chạy lệnh song song nhằm mục đích join các worker vào node master chạy lệnh sau "ansible-playbook playbooks/03-setup-workers.yml"
✅ Kiểm tra kết quả

Kiểm tra trạng thái Jenkins và Kubernetes cluster:
chmod +x health.sh
./health.sh

Kiểm tra node và pod trên master:
kubectl get nodes
kubectl get pods -A

🧹 Dọn dẹp (tuỳ chọn)

Nếu muốn xóa hạ tầng hoặc reset node:

ansible-playbook -i inventory/hosts.yml cleanup.yml

