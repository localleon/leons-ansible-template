# Storing files for ansible 

There are some common practices on how to store files in Ansible! 

## Storing files in the repository 

It's fine to store text-files in the repository. You should however, never commit larger binary files to Git. These stay in your history forever and bloat up the repository size. Files like "X509-CA-Certificates" or configuration files are fine to store! Text files which don't contain any sensitive information like [project/node_playbooks/files/DigiCert_Global_Root_G2.crt](project/node_playbooks/files/DigiCert_Global_Root_G2.crt) are fine. Never store your private certificates unencrypted! 

## Storing large files 

n Ansible, you can use the copy module to copy files from a local or remote source to a destination on the managed node. When referring to an external filepath (a file that exists on your Ansible control machine), you can specify the path using the src parameter. When refering to larger files you should use a external storage device like a NFS-share mounted on all administration workstations: 

```yaml
    - name: Copy example.txt from control machine to managed node
      copy:
        src: /opt/TH-NFS/binary.blob  # Path to the file on the control machine
        dest: /tmp/example.txt  # Destination path on the managed node
```

Or consider downloading your file "on-demand" with ansible: 

```yaml
- name: Download a file from an HTTP URL
  hosts: all
  tasks:
    - name: Download example file from HTTP URL
      get_url:
        url: https://example.com/path/to/file.txt
        dest: /tmp/file.txt
        mode: '0644'  # Optional: Set the permissions of the downloaded file
```