# pyATS Automation Framework

This folder contains pyATS and Genie scripts, testbeds, and data parsers for structured network testing and validation.

pyATS is used to:
- Parse show command outputs
- Validate routing neighbours
- Detect config drift
- Perform continuous pre/post-checks

## Structure
- `scripts/` — Custom test scripts and testcases
- `testbeds/` — YAML-based connection files for all lab routers

## Requirements
- pyATS (latest)
- Genie parsers
- IOS-XR or IOS-XE devices with SSH access
