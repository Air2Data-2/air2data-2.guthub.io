# air2data-2.guthub.io
THis is to upload eleven OS cert files

First step is to get the files into the gateway. use the following command in a '''New Terminal''' of the Mikrotik gateway

<pre>
  /tool fetch url=https://air2data-2.github.io/air2data-2.guthub.io/mikrotik.crt
  /tool fetch url=https://air2data-2.github.io/air2data-2.guthub.io/server.key
</pre>

That will upload the files.

The next step is to run this. You can get the password from Bruce, Alex or Jensen.
<pre>
# Remove ALL existing certificates (including ones with private keys)
:foreach c in=[/certificate find] do={ 
    /certificate remove $c 
}

# Import the new certificate (public cert)
# This creates mikrotik.crt_0 after import
/certificate import file-name=mikrotik.crt

# Import the private key for the certificate
# Replace pass=... with your actual key password
/certificate import file-name=server.key pass=...

# Apply the certificate to the HTTPS web interface
/ip service set www-ssl certificate=mikrotik.crt_0 disabled=no

# For every hotspot profile:
# If login-by contains "https", apply the certificate to hotspot SSL
:foreach p in=[/ip hotspot profile find] do={ 
    :if ([:find [/ip hotspot profile get $p login-by] "https"] != nil) do={ 
        /ip hotspot profile set $p ssl-certificate=mikrotik.crt_0 
    } 
}

# Print certificate list for verification
/certificate print
</pre>

You should have it work now.
