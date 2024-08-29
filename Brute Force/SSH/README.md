alpine-sshd

A lightweight OpenSSH⁠Docker image built atop Alpine Linux. Available on GitHub⁠.

The root password is "root". SSH host keys (RSA, DSA, ECDSA, and ED25519) are auto-generated when the container is started, unless already present.

https://hub.docker.com/r/sickp/alpine-sshd/

Usando el Dockerfile

docker build -t alpine-ssh .

docker run --rm --publish=2222:22 alpine-ssh
