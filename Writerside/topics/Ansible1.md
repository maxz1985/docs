# Ansible1

---

### **1. Ansible, and its main parts**

#### **Key Points**:
- Ansible is an **open-source IT automation tool** used for **configuration management**, **application deployment**, **orchestration**, and **provisioning**.
- It is agentless (no software needs to be installed on target systems) and uses **SSH** for communication.

- Simple to use, with YAML-based configuration (`playbooks`).
- Agentless, so it is straightforward to deploy and maintain.
- Supports declarative and idempotent configurations.

1. **Main Components**:
    - **Inventory**:
        - A list of target systems (hosts) Ansible will manage.
        - Inventory can be static (a `.ini` or `.yaml` file) or dynamic (via scripts or plugins).
        - Example:
          ```ini
          [webservers]
          192.168.1.10
          192.168.1.11
   
          [databases]
          db.example.com
          ```

    - **Playbooks**:
        - YAML files that define a set of tasks to execute on target hosts.
        - Example:
          ```yaml
          - name: Install Apache
            hosts: webservers
            tasks:
              - name: Ensure Apache is installed
                apt:
                  name: apache2
                  state: present
          ```

    - **Modules**:
        - Predefined units of work for tasks (for example, `apt`, `yum`, `service`).
        - Can perform actions like installing software, managing files, or creating users.

    - **Tasks**:
        - Individual steps defined in a playbook that execute a module.

    - **Roles**:
        - A way to organize playbooks into reusable components, including tasks, variables, templates, and files.

    - **Variables**:
        - Used to parameterize playbooks and make them dynamic.

    - **Plugins**:
        - Extend Ansible functionality (for example, connection plugins, callback plugins).

---

- Ansible is agentless, uses YAML for playbooks, and is simpler to set up compared to Puppet or Chef.
- YAML is human-readable and easy to write, making playbooks simple and intuitive.

---

---

### **2. Ansible playbooks**

- A playbook is a **YAML file** that defines a sequence of tasks for Ansible to execute on specified hosts.
- Playbooks are **declarative**: You define the desired state of the system, and Ansible ensures that state.

---

#### **Example Playbook**:
```yaml
- name: Setup Web Servers
  hosts: webservers
  become: yes  # Run tasks as sudo/root
  vars:
    http_port: 80
  tasks:
    - name: Ensure Apache is installed
      apt:
        name: apache2
        state: present

    - name: Start and enable Apache
      service:
        name: apache2
        state: started
        enabled: yes

    - name: Create an index.html file
      copy:
        content: "Welcome to Ansible-managed web server"
        dest: /var/www/html/index.html
```

---

#### **Best Practices for Writing Playbooks**:
1. **Use Roles for Reusability**:
    - Break playbooks into reusable roles (for example, webserver, database, application).
    - Directory structure:
      ```
      roles/
        webserver/
          tasks/
            main.yml
          templates/
            index.html.j2
          vars/
            main.yml
      ```

2. **Avoid Hardcoding**:
    - Use variables for configurable values:
      ```yaml
      vars:
        http_port: 80
      ```

3. **Idempotency**:
    - Write tasks so they only make changes if necessary. For example, `state: present` ensures a package is installed but doesn’t reinstall it if it is already present.

4. **Document Your Playbooks**:
    - Add meaningful comments and use `name` fields for all tasks.

5. **Error Handling**:
    - Use `ignore_errors: yes` for non-critical tasks or conditional checks with `when`.

   Example:
   ```yaml
   - name: Install Apache on Debian
     apt:
       name: apache2
       state: present
     when: ansible_os_family == "Debian"
   ```

---

- Use **Ansible Vault** to encrypt sensitive data (for example, passwords, keys).

---

---

### **3. Ansible roles for organizing playbooks**

- Roles are a **way to organize playbooks and related files** into reusable components.
- Each role contains tasks, variables, templates, files, and handlers specific to the functionality.

#### **Role Directory Structure**:
```
roles/
  webserver/
    tasks/        # Contains the main tasks to run
      main.yml
    handlers/     # Handlers for service restarts, etc.
      main.yml
    templates/    # Jinja2 templates (e.g., index.html.j2)
    files/        # Static files to copy to managed nodes
    vars/         # Variables specific to the role
      main.yml
    defaults/     # Default variables (can be overridden)
      main.yml
    meta/         # Metadata about the role (e.g., dependencies)
```

#### **Example Role**:
1. **Role: `webserver`**:
    - File: `roles/webserver/tasks/main.yml`:
      ```yaml
      - name: Install Apache
        apt:
          name: apache2
          state: present
 
      - name: Ensure Apache is started
        service:
          name: apache2
          state: started
          enabled: yes
      ```

    - File: `roles/webserver/templates/index.html.j2`:
      ```html
      <h1>Welcome to {{ ansible_hostname }}</h1>
      ```

2. **Main Playbook**:
   ```yaml
   - name: Deploy Web Server
     hosts: webservers
     roles:
       - webserver
   ```

---

#### **Advantages of Roles**:
1. **Reusability**:
    - Roles can be reused across multiple projects.

2. **Modularity**:
    - Organizes code into logical components.

3. **Simplifies Playbooks**:
    - Reduces the complexity of main playbooks by delegating tasks to roles.

---

- Define role dependencies in the `meta/main.yml` file of a role:
  ```yaml
  dependencies:
    - role: common
    - role: firewall
  ```

- Use the `vars` section in the playbook to pass variables to the role:
  ```yaml
  roles:
    - { role: webserver, http_port: 8080 }
  ```

---

---

- Modules are designed to only make changes if necessary (for example, `state: present`).

- Ansible Vault to Encrypt sensitive information in playbooks:
      ```bash
      ansible-vault encrypt secrets.yml
      ansible-playbook playbook.yml --ask-vault-pass
      ```

- Use scripts or plugins to handle dynamic inventories of hosts (for example, AWS, Azure, or Kubernetes dynamic inventory).

---
