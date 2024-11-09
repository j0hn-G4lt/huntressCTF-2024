## Obfuscation Station
Unzipping the file provides a ps1 script that needs to be deobfuscated. 
```pwsh
(nEW-objECt  SYstem.iO.COMPreSsIon.deFlaTEStREAm( [IO.mEmORYstreAM][coNVERt]::FROMBAse64sTRING( 'UzF19/UJV7BVUErLSUyvNk5NMTM3TU0zMDYxNjSxNDcyNjexTDY2SUu0NDRITDWpVQIA') ,[io.COmPREssioN.coMpreSSioNmODE]::DeCoMpReSS)| %{ nEW-objECt  sYStEm.Io.StREAMrEADeR($_,[TeXT.encodiNG]::AsCii)} |%{ $_.READTOENd()})| & ( $eNV:cOmSPEc[4,15,25]-JOin'')
```
Can use the following script which takes the string in the original file

```python
import base64
import zlib

# Base64 encoded and compressed string from the PowerShell command
encoded_compressed_str = 'UzF19/UJV7BVUErLSUyvNk5NMTM3TU0zMDYxNjSxNDcyNjexTDY2SUu0NDRITDWpVQIA'

# Decode from Base64
compressed_data = base64.b64decode(encoded_compressed_str)

# Decompress the data
try:
    decompressed_data = zlib.decompress(compressed_data, -zlib.MAX_WBITS)
    decoded_output = decompressed_data.decode('ascii')
except Exception as e:
    decoded_output = str(e)

print(decoded_output)
```

`$5GMLW = "flag{3ed675ef0343149723749c34fa910ae4}"`
