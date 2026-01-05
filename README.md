# ubuntu-server-hardening

A repo that hardens Ubuntu Server 24.04.

To do this, clone this repo and execute the script, or use a copy-and-paste command:

```sh
# update all packages
sudo apt update
sudo apt upgrade -y

# unattended-upgrades
sudo apt install unattended-upgrades -y
sudo systemctl enable --now unattended-upgrades
sudo systemctl enable --now apt-daily.timer
sudo systemctl enable --now apt-daily-upgrade.timer

# openscap
sudo apt install -y openscap-scanner openscap-utils ssg-base ssg-applications ssg-debderived
cd ~
git clone https://github.com/ComplianceAsCode/content.git
cd content
sudo apt install -y cmake make python3 python3-jinja2 libxml2-utils xsltproc
./build_product ubuntu2404
cd ~
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_cis_level1_server \
  --report before_hardening.html \
  ~/content/build/ssg-ubuntu2404-ds.xml
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_cis_level1_server \
  --remediate \
  ~/content/build/ssg-ubuntu2404-ds.xml
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_cis_level1_server \
  --report after_hardening.html \
  ~/content/build/ssg-ubuntu2404-ds.xml
```

What this does is:
- updates all packages with `apt`,
- sets up `unattended-upgrades` to auto-update them going forward
- loads [this repo](https://github.com/ComplianceAsCode/content),
    - builds it
    - creates a hardening status report BEFORE hardening
    - _hardens_ using a `content_profile_cis_level1_server` hardening profile
    - creates a hardening report AFTER hardening

More information on what this _actually does_ available [here](https://complianceascode.github.io/content-pages/guides/ssg-ubuntu2404-guide-cis_level1_server.html).