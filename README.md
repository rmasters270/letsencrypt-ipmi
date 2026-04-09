# Let's Encrypt IPMI

[![release](https://github.com/rmasters270/etsencrypt-ipmi/actions/workflows/release.yml/badge.svg?branch=main)](https://github.com/rmasters270/letsencrypt-ipmi/actions/workflows/release.yml)

Docker container to install IPMI TLS certificates via ACME

---

## Project Status

This repository was originally forked from:

* <https://github.com/marthydavid/supermicro-letsencrypt>

It has since **diverged significantly** and is no longer a drop-in replacement for the original project.

### Enhancements in this version

* Added support for secrets in files
* Added support for reoccurring schedule
* Added support for **ASRock Rack IPMI** certificate updates
* Unified certificate handling across multiple IPMI vendors
* Integrated additional scripting and automation workflows
* Improved compatibility with different certificate formats (full chain vs device-specific requirements)

ASRock support is based on:

* <https://github.com/khung/letsencrypt-scripts/blob/master/asrock_ipmi_cert_updater.py>

---

## Config options

| Option        | Type                                                                               | Default                                          |
|---------------|------------------------------------------------------------------------------------|--------------------------------------------------|
| IPMI_USERNAME | String                                                                             | -                                                |
| IPMI_PASSWORD | String                                                                             | -                                                |
| IPMI_DOMAIN   | String                                                                             | -                                                |
| LE_EMAIL      | String                                                                             | -                                                |
| LE_SERVER     | String                                                                             | <https://acme-v02.api.letsencrypt.org/directory> |
| DNS_PROVIDER  | String (options of [go-acme/lego](https://github.com/go-acme/lego#dns-providers) ) | route53                                          |
| MANUFACTURER  | String (Supermicro, ASRock)                                                        | -                                                |
| MODEL         | String (X9-X13)                                                                    | X11                                              |
| FORCE_UPDATE  | bool                                                                               | false                                            |
| DEBUG         | any                                                                                | -                                                |

Append _FILE to any variable to read its value from a file instead of directly from the environment.

---

## Usage with Docker

```bash
docker run -v ~/.aws:/home/lego/.aws \
           -v ~/.lego:/home/lego/.lego \
           -e IPMI_USERNAME=ADMIN \
           -e IPMI_PASSWORD=ADMIN \
           -e IPMI_ADDRESS=ipmi.my.tld \
           -e LE_EMAIL=me@my.tld \
           -e DNS_PROVIDER=route53 \
           -e MANUFACTURER=Supermicro
           -e MODEL=X10 \
           ghcr.io/rmasters270/letsencrypt-ipmi
```

---

## Usage with Kubernetes CronJob

```bash
kubectl create configmap sm-ipmi-info \
        --from-literal=IPMI_USERNAME=ADMIN \
        --from-literal=IPMI_DOMAIN=ipmi.my.tld \
        --from-literal=LE_EMAIL=me@my.tld \
        --from-literal=DNS_PROVIDER=route53 \
        --from-literal=MANUFACTURER=Supermicro
        --from-literal=MODEL=X10

kubectl create secret generic sm-ipmi-secret \
        --from-literal=IPMI_PASSWORD=ADMIN \
        --from-literal=AWS_ACCESS_KEY_ID=blahblahblah \
        --from-literal=AWS_SECRET_ACCESS_KEY=blahblahblah

kubectl apply -f demo/kubernetes/cronjob.yaml
kubectl get cm,secret,cronjob

# To trigger a run:
kubectl create job --from cronjob/letsencrypt letsencrypt-first-run

kubectl logs -f letsencrypt-first-run
```

---

## Licensing

This project is distributed under the terms of the GNU General Public License v2.

### Why GPLv2?

This repository includes code derived from the Supermicro IPMI certificate updater originally written by Jari Turkia and distributed under GPLv2. Because of this, the combined work must also be distributed under GPLv2.

### Third-Party Components

* Original project:
  <https://github.com/marthydavid/supermicro-letsencrypt> (MIT License)

* Supermicro IPMI updater:
  Copyright (c) Jari Turkia
  Licensed under GPLv2

* ASRock IPMI updater script:
  <https://github.com/khung/letsencrypt-scripts>
  Released into the public domain (or equivalent permissive terms)

See the `NOTICE` file for full attribution details.

---

## Notes

* This project is **not fully compatible with upstream** due to architectural and licensing changes
* It is intended for multi-vendor IPMI environments (Supermicro + ASRock Rack)
* No affiliation with Supermicro or ASRock Rack

---

## Disclaimer

Use at your own risk. Modifying IPMI firmware or certificates can lead to loss of remote access if performed incorrectly.
