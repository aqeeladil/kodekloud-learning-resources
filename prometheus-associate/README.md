# Prometheus Certified Associate (PCA)

Some important certification details are listed below:

- You will have 1.5 hours to complete the exam.
- The certification is valid for 3 years.
- This exam is online, proctored with multiple-choice questions.
- One retake is available for this exam.

## Important links:

- Prometheus Certified Associate(PCA) registration link: `https://training.linuxfoundation.org/certification/prometheus-certified-associate/`

- Exam curriculum: `https://github.com/cncf/curriculum/blob/master/PCA_Curriculum.pdf`

- Certification FAQs: `https://docs.linuxfoundation.org/tc-docs/certification/frequently-asked-questions-pca`

- Candidate Handbook: `https://docs.linuxfoundation.org/tc-docs/certification/lf-handbook2`

- To ensure your system meets the exam requirements, visit this link: `https://syscheck.bridge.psiexams.com/`

- Important exams instructions to check before scheduling the exam: `https://docs.linuxfoundation.org/tc-docs/certification/important-instructions-pca`

## Prometheus Installation

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.40.1/prometheus-2.40.1.linux-amd64.tar.gz

tar xvf prometheus-2.40.1.linux-amd64.tar.gz

cd prometheus-2.40.1.linux-amd64

./prometheus > /dev/null 2>&1 &

curl localhost:9090/metrics
```

```bash
# Add Prometheus user as below:
useradd --no-create-home --shell /bin/false prometheus

# Create Directories for storing prometheus config file and data:
mkdir /etc/prometheus
mkdir /var/lib/prometheus

# Change the permissions:
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus

# Copy the binaries:
cp /root/prometheus-2.40.1.linux-amd64/prometheus /usr/local/bin/
cp /root/prometheus-2.40.1.linux-amd64/promtool /usr/local/bin/

# Change the ownership of binaries:
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool

# Copy the directories consoles and console_libraries:
cp -r /root/prometheus-2.40.1.linux-amd64/consoles /etc/prometheus
cp -r /root/prometheus-2.40.1.linux-amd64/console_libraries /etc/prometheus

# Change the ownership of directories consoles and console_libraries:
chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries

# Move prometheus.yml file to /etc/prometheus directory:
cp /root/prometheus-2.40.1.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml

# Change the ownership of file /etc/prometheus/prometheus.yml:
chown prometheus:prometheus /etc/prometheus/prometheus.yml

# Create a service for prometheus:
vi /etc/systemd/system/prometheus.service
# Add below lines in it:
```

```bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

```bash
# Run below commands:

systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
```

## Install Node Exporters

```bash
ssh node01

wget https://github.com/prometheus/node_exporter/releases/download/v1.4.0/node_exporter-1.4.0.linux-amd64.tar.gz
tar xvf node_exporter-1.4.0.linux-amd64.tar.gz

cd node_exporter-1.4.0.linux-amd64

./node_exporter > /dev/null 2>&1 &
```

```bash
ssh node02

wget https://github.com/prometheus/node_exporter/releases/download/v1.4.0/node_exporter-1.4.0.linux-amd64.tar.gz

tar xvf node_exporter-1.4.0.linux-amd64.tar.gz

cd node_exporter-1.4.0.linux-amd64

./node_exporter > /dev/null 2>&1 &

curl localhost:9100/metrics
```

```bash
# Update the /etc/prometheus/prometheus.yml file to add a job called nodes to start scraping the two node_exporters. 
vi /etc/prometheus/prometheus.yml

# Add below code under scrape_configs::

- job_name: "nodes"
  static_configs:
    - targets: ['node01:9100', 'node02:9100']


# Restart the process with below command:
systemctl restart prometheus
```
