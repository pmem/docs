# NDCTL Changelog

The master index can be found at [https://github.com/pmem/ndctl/releases](https://github.com/pmem/ndctl/releases)

## v65.0 - 9th May, 2019

This release incorporates functionality up to the 5.1 kernel, and adds a number of bug fixes and improvements.

Highlights include a new command to clear errors on a given namespace, a new travis YAML configuration to enable travis builds for Ubuntu, an example QEMU script in contrib/ for HMAT emulation, an optional poll interval for wait-scrub, several fixes related to the security commands, support for the HYPER-V family of DSM commands, and several fixes to tests, documentation, and related to building.

Commands: 

* clear-errors: new command to clear errors on a namespace 
* monitor: remove the requirement of a default config 
* sanitize-dimm: allow a zero-key for secure-erase 
* sanitize-dimm: preserve keys after an overwrite 
* load-keys: fix for non-TPM keys

Tests: 

* security: add a new testlet for load-keys 
* test-core: add dax\_pmem\* modules 
* misc: fix sys/mman.h vs linux/mman.h includes

APIs: 

* ndctl\_bus\_poll\_scrub\_completion

## v64.1 - 5th February, 2019

**Fixes:** 

* Fix build issues around keyutils inclusion

## v64.0 - 2nd February, 2019

This release incorporates functionality up to the 5.0 kernel, and adds a number of bug fixes and improvements.

Highlights include a migration path for the new dax-bus ABI, several cleanups to ndctl-monitor, support for firmware status translation, displaying the supported\_alignments attribute, and using it in the namespace creation process, and support for security operations as defined in the Intel DSM specification.

**Commands:** 

* inject-smart: check the firmware status for errors 
* zero-labels: correctly handle firmware errors 
* create-namespace: use supported\_alignments when available 
* Add new security commands

**Tests:** 

* security.sh: new test for security commands 
* device-dax: fix intermittent poison handling failures 
* dsm-fail: cleanup leftover debug

**APIs:** 

* ndctl\_cmd\_submit\_xlat 
* ndctl\_cmd\_xlat\_firmware\_status 
* ndctl\_dax\_get\_num\_alignments 
* ndctl\_dax\_get\_supported\_alignment 
* ndctl\_dimm\_disable\_passphrase 
* ndctl\_dimm\_freeze\_security 
* ndctl\_dimm\_get\_security 
* ndctl\_dimm\_master\_secure\_erase 
* ndctl\_dimm\_overwrite 
* ndctl\_dimm\_secure\_erase 
* ndctl\_dimm\_update\_master\_passphrase 
* ndctl\_dimm\_update\_passphrase 
* ndctl\_dimm\_wait\_overwrite 
* ndctl\_pfn\_get\_num\_alignments 
* ndctl\_pfn\_get\_supported\_alignment

## v63.0 - 7th October, 2018

This release incorporates functionality up to the 4.20 kernel, and a number of bug fixes and improvements.

Highlights include switching the documentation build to asciidoctor by default, fixes to destroy-namespace for reporting the number of namespaces acted upon, using the namespace badblocks listings exported by the kernel, and making them available to non-root users, a new helper for retrieving the dirty-shutdown-count, reverting the udev rule to set the shutdown count latch and cache the dirty-shutdown-count, and fixing the ndctl-monitor daemon to exit successfully in the absence of NVDIMMs.

**Commands**: 

* destroy-namespace: fix number of namespaces reported 
* check-labels: fix the number of labels checked reporting 
* monitor: exit daemon with success when no DIMMs found

**Tests**: 

* Fix a missing include for list\_smart\_dimm 
* pfn-meta-errors.sh: new test for clearing errors in the volatile 'struct page' metadata area

**APIs**: 

* ndctl\_dimm\_get\_dirty\_shutdown 
* ndctl\_namespace\_get\_first\_badblock 
* ndctl\_namespace\_get\_next\_badblock

## v62.0 - 9th July, 2018

This release incorporates functionality up to the 4.19 kernel, and a number of bug fixes and improvements.

Highlights include addition of the 'ndctl monitor' command to monitor for SMART health events, use of the new max\_available\_extent sysfs attribute for namespace creation, verbosity levels for ndctl-list, a udev rule for enabling the LSS latch when supported, a bypass route for making the unsafe shutdown count available for non-privileged users, improvements to ndctl-inject-smart that include an 'uninject' option for all fields, and a new unit test, a number of static analysis fixes, and unit test improvements and fixes.

**Commands:**

* monitor: new command for monitoring SMART health events 
* list: support -v, -vv, -vvv verbosity levels 
* inject-smart: add --uninject- and --uninject-all options 
* create-namespace: use maxavailable\_extent for namespace creation 
* list: add new fields to -H for alarm\_enabled 
* list: always output JSON arrays when --human is absent

**Tests:**

* dax.sh: dax-poisonCheck for availability of MAP\_SYNC 
* dax.sh: fix return code 
* device-dax: relax canned timeouts 
* monitor: new test 
* inject-smart: new test 
* max\_available\_extent\_ns: new test

**APIs:**

* ndctl\_cmd\_smart\_inject\_ctrl\_temperature 
* ndctl\_dimm\_get\_event\_flags 
* ndctl\_dimm\_get\_flags 
* ndctl\_dimm\_get\_health 
* ndctl\_dimm\_is\_flag\_supported 
* ndctl\_dimm\_smart\_inject\_supported 
* ndctl\_region\_get\_max\_available\_extent

## v61.2 - 6th July, 2018

**Fixes:**

* libndctl: fix the uninject API \(v1\) actually injecting errors

## v61.1 - 27th June, 2018

**Fixes:**

* Add autotools detection for MAP\_SYNC

## v61.0 - 26th June, 2018

This release incorporates functionality up to the 4.18 kernel, and a number of bug fixes and improvements.

Highlights include a fix to the error injection APIs to inject fewer bytes of errors per sector, support for building documentation with asciidoctor in addition to asciidoc, multi-arrgument support for util\_\_filter, and a new OPTION\_FILENAME in option parsing. Unit test updates include cleanups to unit test scripts refactoring out a lot of common boilerplate, MADV\_HWPOISON tests, and a new test for capacity vs label locking.

**Commands:**

* inject-error: add a --saturate option to inject entire sectors 
* list: display the 'map' location in namespace listings 
* list: add controller temperature, and its threshold/alarm setting

**Tests:**

* dax-pmd, device-dax: add a test for MADV\_HWPOISON 
* sector-mode.sh: fix to work with updated label support in nfit\_test 
* common: source common bash functions and variables 
* dsm-fail: test for capacity vs label locking 
* libndctl: update for smart controller temperature 
* various: disable tests that inject poison with dax until 4.19

**APIs:**

* ndctl\_cmd\_ars\_cap\_get\_clear\_unit 
* ndctl\_cmd\_ars\_stat\_get\_flag\_overflow 
* ndctl\_namespace\_inject\_error2 
* ndctl\_namespace\_uninject\_error2

## v60.3 - May 17th, 2018

**Fixes:**

* ndctl: fix libtool versioning

## v60.2 - May 16th, 2018

**Fixes:**

* inject-error: inject only 'clear\_err\_unit' bytes of error per sector

## v60.1 - April 23rd, 2018

**Fixes:**

* documentation: add inject-smart to the Makefile 
* libndctl: fix ABI breakage due to rename of fw\_info\_get\_updated\_version

## v60 - April 17th, 2018

**Added:**

This release incorporates functionality up to the 4.17 kernel, and a number of bug fixes and improvements.

Highlights include ack\_shutdown\_count support, fixes to tests that performed error injections, refactor core topology walking into util\_filter\_walk\(\), a new test for partition auto detection for btt/blk namespacees, numa\_node support for regions, cleanups to the firmware update command, removal of daxctl io, support for persistence domains for buses and regions, APIs for retrieving and setting the write\_cache attribute for namespacees, fixes to ARS APIs, new ARS control commands in ndctl, and an API for the deep\_flush attribute for regions.

**Commands:**

* ndctl list: option to display firmware information 
* ndctl create-namespace: fix minimum alignment detection 
* ndctl list: allow filtereing by numa node 
* ndctl list: fix sector\_size sometimes showing as -1 
* daxctl io: remove as this functionality is provided in PMDK 
* ndctl update-firmware: fix DSM input/output sizes various: replace direct errno prints with strerror strings 
* ndctl read-labels: fix json reference counting 
* various: replace refrences to 'memory' or 'dax' with 'devdax' or 'fsdax' 
* ndctl list: report the bus scrub state 
* ndctl {wait,start}-scrub: new commands for ARS control 
* ndctl list: add a raw\_uuid field to namespace listings

**Tests:**

* ack-shutdown-count-set: new test for the shutdown count APIs 
* various: ensure we use the locally build 'ndctl' 
* various: fix usage of error injection commands for older kernels 
* btt-pad-compat: fix stale json being reused for future commands 
* btt-pad-compat: explicitly request namespace size 
* dpa-alloc: fix for kernels with 4M min namespace size 
* btt-pad-compat: skip for pre-4k capable kernels 
* firmware-update: remove fallocate 
* rescan-partitions: new test for autodetection of partitions 
* core: fix module taint sanity check 
* libndctl: add write\_cache testing in check\_nameespaces\(\) 
* pmem-errors: fix locking vs new ARS reworks

**API's:**

* ndctl\_bus\_get\_persistence\_domain 
* ndctl\_bus\_get\_scrub\_state 
* ndctl\_bus\_start\_scrub 
* ndctl\_cmd\_fw\_info\_get\_next\_version 
* ndctl\_dimm\_cmd\_new\_ack\_shutdown\_count 
* ndctl\_dimm\_fw\_update\_supported 
* ndctl\_namespace\_disable\_write\_cache 
* ndctl\_namespace\_enable\_write\_cache 
* ndctl\_namespace\_write\_cache\_is\_enabled 
* ndctl\_region\_deep\_flush 
* ndctl\_region\_get\_numa\_node 
* ndctl\_region\_get\_persistence\_domain

## v59.3 - March 27th, 2018

**Fixes:**

* create-namespace: fix minimum alignment detection 
* list: fix sector\_size listing 
* API, smart: fix threshold temperature helper 
* update-firmware: fix input/output size for NVDIMM\_FAMILY\_INTEL 
* update-firmware: kill usage of flock\(\) in verify\_fw\_file\(\) 
* test: fix module-taint sanity-check

## v59.2 - February 9th, 2018

**Fixes:**

* Unit test fixups for package build environments.

## v59.1 - February 9th, 2018

**Fixes:**

* Compile fixes for ARM and PowerPC

## v59.0 - February 9th, 2018

This release incorporates functionality up to the 4.16 kernel, and a number of bug fixes and improvements.

Highlights include new ACPI error injection DSM support, a variety of smart enhancements that include getting and setting thresholds, injecting smart attribute values and flags, support for firmware update, and fixes for a BTT padding incompatibility.

**Commands:**

* ndctl inject-error - new command for media error injection 
* ndctl disable-region - check for mounted namespaces 
* ndctl {create,destroy}-namespace - clarify --force option 
* ndctl create-namespace - clarify autolabel failures and fallback 
* ndctl list - use 'fsdax' and 'devdax' modes 
* ndctl update-firmware - new command for firmware update 
* ndctl inject-smart - new command for setting smart thresholds and injecting attributes

**Tests:**

* inject-error: new test for the error injection interfaces 
* btt-errors: new test for media error handling in the BTT 
* smart-listen: test for listening for smart triggers 
* smart-notify: generate smart notifications 
* hugetlb: test hugetlb faults 
* btt-pad-incompat: regression test for the old and new versions of the btt log padding format 
* update-firmware: test the firmware update process

**API's:**

* ndctl\_bb\_get\_block 
* ndctl\_bb\_get\_count 
* ndctl\_bus\_get\_scrub\_count 
* ndctl\_bus\_has\_error\_injection 
* ndctl\_bus\_wait\_for\_scrub\_completion 
* ndctl\_cmd\_fw\_fquery\_get\_fw\_rev 
* ndctl\_cmd\_fw\_info\_get\_max\_query\_time 
* ndctl\_cmd\_fw\_info\_get\_max\_send\_len 
* ndctl\_cmd\_fw\_info\_get\_query\_interval 
* ndctl\_cmd\_fw\_info\_get\_run\_version 
* ndctl\_cmd\_fw\_info\_get\_storage\_size 
* ndctl\_cmd\_fw\_info\_get\_updated\_version 
* ndctl\_cmd\_fw\_start\_get\_context 
* ndctl\_cmd\_fw\_xlat\_firmware\_status 
* ndctl\_cmd\_smart\_get\_ctrl\_temperature 
* ndctl\_cmd\_smart\_get\_media\_temperature 
* ndctl\_cmd\_smart\_get\_shutdown\_count 
* ndctl\_cmd\_smart\_inject\_fatal 
* ndctl\_cmd\_smart\_inject\_media\_temperature 
* ndctl\_cmd\_smart\_inject\_spares 
* ndctl\_cmd\_smart\_inject\_unsafe\_shutdown 
* ndctl\_cmd\_smart\_threshold\_get\_ctrl\_temperature 
* ndctl\_cmd\_smart\_threshold\_get\_media\_temperature 
* ndctl\_cmd\_smart\_threshold\_get\_supported\_alarms 
* ndctl\_cmd\_smart\_threshold\_set\_alarm\_control 
* ndctl\_cmd\_smart\_threshold\_set\_ctrl\_temperature ndctl\_cmd\_smart\_threshold\_set\_media\_temperature 
* ndctl\_cmd\_smart\_threshold\_set\_spares 
* ndctl\_cmd\_smart\_threshold\_set\_temperature 
* ndctl\_decode\_smart\_temperature 
* ndctl\_dimm\_aliased ndctl\_dimm\_cmd\_new\_fw\_abort 
* ndctl\_dimm\_cmd\_new\_fw\_finish 
* ndctl\_dimm\_cmd\_new\_fw\_finish\_query 
* ndctl\_dimm\_cmd\_new\_fw\_get\_info 
* ndctl\_dimm\_cmd\_new\_fw\_send 
* ndctl\_dimm\_cmd\_new\_fw\_start\_update 
* ndctl\_dimm\_cmd\_new\_smart\_inject 
* ndctl\_dimm\_cmd\_new\_smart\_set\_threshold 
* ndctl\_dimm\_locked ndctl\_encode\_smart\_temperature 
* ndctl\_namespace\_inject\_error 
* ndctl\_namespace\_injection\_get\_first\_bb 
* ndctl\_namespace\_injection\_get\_next\_bb 
* ndctl\_namespace\_injection\_status 
* ndctl\_namespace\_uninject\_error

