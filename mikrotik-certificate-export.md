
```
# Certificate export tool for ROS 7.x by 611
# from Mikrotik forum

# Filter for cert names
:local certnamesearchstring "";

# Create script file for cert import
:local scriptname "cert-import-$certnamesearchstring.rsc";
:put "Exporting (non-expired, non-revoked) certificates with filter '$certnamesearchstring', writing import script to $scriptname:";
# There could be a lot of files, so /file print is suboptimal, we'll use /certificate instead with a filter, so that the output will be empty (except for header)
/certificate print file=$scriptname where !name;

# Export loop (with reqd variables)
:local certname; :local exportpass;
:local scriptbody ""; :local scriptpart 1;

# Certificate templates won't export and have no "invalid-after" date, excluding them 
# You may add extra conditions to filter the cert list here:
:foreach i in=[/certificate find invalid-after !expired !revoked name~($certnamesearchstring)] do={
  :set $certname [/certificate get $i name];
  :set $exportpass [:rndstr from="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789" length=32]
  :put "* $certname";

  # Actual export, ignore errors
  :do { /certificate export-certificate $i file-name=$certname export-passphrase=$exportpass; } on-error={ :put "  ! Error"; }

```
