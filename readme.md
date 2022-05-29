# Authenticode-JS

Authenticode-JS is a fully NodeJS tool that can sign Windows executables. You can also get information about an executable signature, un-sign an executable and generate your own code signing certificate.

Running the tool will show this:

```
MeshCentral Authenticode Tool.
Usage:
  node authenticode.js [command] [options]
Commands:
  info: Show information about an executable.
          --json                   Show information in JSON format.
  sign: Sign an executable.
          --exe [file]             Required executable to sign.
          --out [file]             Resulting signed executable.
          --pem [pemfile]          Certificate & private key to sign the executable with.
          --desc [description]     Description string to embbed into signature.
          --url [url]              URL to embbed into signature.
  unsign: Remove the signature from the executable.
          --exe [file]             Required executable to un-sign.
          --out [file]             Resulting executable with signature removed.
  createcert: Create a code signging self-signed certificate and key.
          --out [pemfile]          Required certificate file to create.
          --cn [value]             Required certificate common name.
          --country [value]        Certificate country name.
          --state [value]          Certificate state name.
          --locality [value]       Certificate locality name.
          --org [value]            Certificate organization name.
          --ou [value]             Certificate organization unit name.
          --serial [value]         Certificate serial number.

Note that certificate PEM files must first have the signing certificate,
followed by all certificates that form the trust chain.
```

## License
This software is licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).