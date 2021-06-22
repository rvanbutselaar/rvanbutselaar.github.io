OCP 4 sshd hardening
========================================================================

To only accept the following ssh ciphers you will need to configure this.

.. code-block:: bash

  Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-c%

You need 2 MachineConfig's. One for the master:

.. code-block:: yaml

  apiVersion: machineconfiguration.openshift.io/v1
  kind: MachineConfig
  metadata:
    labels:
      machineconfiguration.openshift.io/role: "master"
    name: "master-ssh-hardening"
  spec:
    config:
      ignition:
        config: {}
        security:
          tls: {}
        timeouts: {}
        version: 3.2.0
      networkd: {}
      passwd: {}
      storage:
        files:
        - contents:
            source: data:text/plain;charset=utf-8;base64,Q2lwaGVycyBjaGFjaGEyMC1wb2x5MTMwNUBvcGVuc3NoLmNvbSxhZXMyNTYtZ2NtQG9wZW5zc2guY29tLGFlczEyOC1nY21Ab3BlbnNzaC5jb20sYWVzMjU2LWN0cixhZXMxOTItY3RyLGFlczEyOC1jdHI=
          filesystem: root
          mode: 420
          overwrite: true
          path: /etc/ssh/sshd_config.d/99-ssh-hardening.conf
        - contents:
            source: data:text/plain;charset=utf-8;base64,IyBDb25maWd1cmF0aW9uIGZpbGUgZm9yIHRoZSBzc2hkIHNlcnZpY2UuCgojIFRoZSBzZXJ2ZXIga2V5cyBhcmUgYXV0b21hdGljYWxseSBnZW5lcmF0ZWQgaWYgdGhleSBhcmUgbWlzc2luZy4KIyBUbyBjaGFuZ2UgdGhlIGF1dG9tYXRpYyBjcmVhdGlvbiwgYWRqdXN0IHNzaGQuc2VydmljZSBvcHRpb25zIGZvcgojIGV4YW1wbGUgdXNpbmcgIHN5c3RlbWN0bCBlbmFibGUgc3NoZC1rZXlnZW5AZHNhLnNlcnZpY2UgIHRvIGFsbG93IGNyZWF0aW9uCiMgb2YgRFNBIGtleSBvciAgc3lzdGVtY3RsIG1hc2sgc3NoZC1rZXlnZW5AcnNhLnNlcnZpY2UgIHRvIGRpc2FibGUgUlNBIGtleQojIGNyZWF0aW9uLgoKIyBEbyBub3QgY2hhbmdlIHRoaXMgb3B0aW9uIHVubGVzcyB5b3UgaGF2ZSBoYXJkd2FyZSByYW5kb20KIyBnZW5lcmF0b3IgYW5kIHlvdSBSRUFMTFkga25vdyB3aGF0IHlvdSBhcmUgZG9pbmcKClNTSF9VU0VfU1RST05HX1JORz0wCiMgU1NIX1VTRV9TVFJPTkdfUk5HPTEKCiMgU3lzdGVtLXdpZGUgY3J5cHRvIHBvbGljeToKIyBUbyBvcHQtb3V0LCB1bmNvbW1lbnQgdGhlIGZvbGxvd2luZyBsaW5lCkNSWVBUT19QT0xJQ1k9
          filesystem: root
          mode: 420
          overwrite: true
          path: /etc/sysconfig/sshd
    osImageURL: ""

And one for the worker node(s)

.. code-block:: yaml

  apiVersion: machineconfiguration.openshift.io/v1
  kind: MachineConfig
  metadata:
    labels:
      machineconfiguration.openshift.io/role: "worker"
    name: "worker-ssh-hardening"
  spec:
    config:
      ignition:
        config: {}
        security:
          tls: {}
        timeouts: {}
        version: 3.2.0
      networkd: {}
      passwd: {}
      storage:
        files:
        - contents:
            source: data:text/plain;charset=utf-8;base64,Q2lwaGVycyBjaGFjaGEyMC1wb2x5MTMwNUBvcGVuc3NoLmNvbSxhZXMyNTYtZ2NtQG9wZW5zc2guY29tLGFlczEyOC1nY21Ab3BlbnNzaC5jb20sYWVzMjU2LWN0cixhZXMxOTItY3RyLGFlczEyOC1jdHI=
          filesystem: root
          mode: 420
          overwrite: true
          path: /etc/ssh/sshd_config.d/99-ssh-hardening.conf
        - contents:
            source: data:text/plain;charset=utf-8;base64,IyBDb25maWd1cmF0aW9uIGZpbGUgZm9yIHRoZSBzc2hkIHNlcnZpY2UuCgojIFRoZSBzZXJ2ZXIga2V5cyBhcmUgYXV0b21hdGljYWxseSBnZW5lcmF0ZWQgaWYgdGhleSBhcmUgbWlzc2luZy4KIyBUbyBjaGFuZ2UgdGhlIGF1dG9tYXRpYyBjcmVhdGlvbiwgYWRqdXN0IHNzaGQuc2VydmljZSBvcHRpb25zIGZvcgojIGV4YW1wbGUgdXNpbmcgIHN5c3RlbWN0bCBlbmFibGUgc3NoZC1rZXlnZW5AZHNhLnNlcnZpY2UgIHRvIGFsbG93IGNyZWF0aW9uCiMgb2YgRFNBIGtleSBvciAgc3lzdGVtY3RsIG1hc2sgc3NoZC1rZXlnZW5AcnNhLnNlcnZpY2UgIHRvIGRpc2FibGUgUlNBIGtleQojIGNyZWF0aW9uLgoKIyBEbyBub3QgY2hhbmdlIHRoaXMgb3B0aW9uIHVubGVzcyB5b3UgaGF2ZSBoYXJkd2FyZSByYW5kb20KIyBnZW5lcmF0b3IgYW5kIHlvdSBSRUFMTFkga25vdyB3aGF0IHlvdSBhcmUgZG9pbmcKClNTSF9VU0VfU1RST05HX1JORz0wCiMgU1NIX1VTRV9TVFJPTkdfUk5HPTEKCiMgU3lzdGVtLXdpZGUgY3J5cHRvIHBvbGljeToKIyBUbyBvcHQtb3V0LCB1bmNvbW1lbnQgdGhlIGZvbGxvd2luZyBsaW5lCkNSWVBUT19QT0xJQ1k9
          filesystem: root
          mode: 420
          overwrite: true
          path: /etc/sysconfig/sshd
    osImageURL: ""