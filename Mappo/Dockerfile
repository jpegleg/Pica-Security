FROM kalilinux/kali-rolling
RUN apt-get update && \
    apt-get install -y nmap
COPY network.in /tmp/network.in    
CMD while true; do nmap -vvv -p- -A $(cat /tmp/network.in); done
