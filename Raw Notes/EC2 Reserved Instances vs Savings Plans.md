---
tags:
  - cloud/aws/ec2
  - pricing
---

---

#### Key Notes
 * EC2 Instance Savings Plans provide nearly the same pricing as Standard RIs, which is why a t3.large Linux 3-year No Upfront for example would often shows the same rate.
 * The main advantage of Savings Plans is **flexibility, not additional savings**.


| Feature                | Standard RI                                                         | EC2 Instance Savings Plan                                      |
| ---------------------- | ------------------------------------------------------------------- | -------------------------------------------------------------- |
| Pricing                | Up to ~72% off                                                      | Up to ~72% off                                                 |
| Commitment Type        | Specific instance configuration<br>exp: t3.large,linux,eu-central-1 | Specific instance family + region<br>exp: t family,u-central-1 |
| Change Instance Size   | Limited                                                             | ✅                                                              |
| Change OS              | Limited                                                             | ✅                                                              |
| Change Tenancy         | Limited                                                             | ✅                                                              |
| Change Instance Family | ❌                                                                   | ❌                                                              |
| Change Region          | ❌                                                                   | ❌                                                              |
| Covers Fargate/Lambda  | ❌                                                                   | ❌                                                              |
| Capacity Reservation   | ✅ (Zonal RI)                                                        | ❌                                                              |