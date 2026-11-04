Security questions

1. --pid=host removes PID isolation. The container can see host processes, which is dangerous because it may access or interfere with important system processes.
2. Running a container as root is dangerous because an attacker could gain high privileges. Fix: create a non-root user and use the USER instruction in the Dockerfile.
3. ●    The image could contain malware or malicious code.
    * It could contain security vulnerabilities because the publisher is unknown.
4. Every production container should have a memory limit to prevent it from consuming all the host RAM. This protects other applications and prevents system crashes.
