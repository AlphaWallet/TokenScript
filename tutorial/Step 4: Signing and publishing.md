To publish your Tokenscript, it has to be signed.

Currently, a TokenScript is signed by an SSL key. For example, a TokenScript signed by the SSL key for the domain name shong.wang is displayed as "reputation relies on shong.wang" when rendered. To make your first signing easy, we provided both the signing key and needed SSL certificates.

# Install the tools

All XML signing tool should work, while we demonstrate here a specific tool xmlsectool.

First, check that you have the right tool installed. Say, in OS X with brew installed:

    $ brew install xmlsectool

If your package manager doesn't provide xmlsectool, you can download from its [home page](https://wiki.shibboleth.net/confluence/display/XSTJ2/xmlsectool+V2+Home) and install it manually.

From here I'll assume your system supports make. If not, go to [I don't use Make](#i-dont-use-make) section.

If you installed xmlsectool manually, for example to `/opt/xmlsectool-2.0.0/`, you will need to change the first line of the MakeFile to this:

XMLSECTOOL=/opt/xmlsectool-2.0.0/xmlsectool.sh

Having that sorted out, please also install xmlstarlet and xmllint

    $ brew install xmlstarlet xmllint

Now we have all the tools ready.

# Validate the test TokenScript

Do a `make`. It will validate AdmissionTicket.xml. It should pass if you haven't changed it.

    $ make
    # XML Canonicalization
    xmlstarlet c14n AdmissionTicket.xml  > AdmissionTicket.canonicalized.xml
    # XML Validation
    xmlstarlet val --xsd ../../schema/tokenscript.xsd AdmissionTicket.canonicalized.xml || (xmllint --noout --schema ../../schema/tokenscript.xsd AdmissionTicket.canonicalized.xml; rm AdmissionTicket.canonicalized.xml)
    AdmissionTicket.canonicalized.xml - valid

# Sign the test TokenScript

We prepared the signing key and SSL certificates in ssl directory. If you use yours, remember to replace their references in the Makefile

Run `make` with `AdmissionTicket.tsml`

    $ make AdmissionTicket.tsml
    /opt/xmlsectool-2.0.0/xmlsectool.sh --sign --keyInfoKeyName 'Shong Wang' --digest SHA-256 --signatureAlgorithm http://www.w3.org/2001/04/xmldsig-more#rsa-sha256 --inFile AdmissionTicket.canonicalized.xml --outFile AdmissionTicket.tsml --key ssl/shong.wang.key --certificate ssl/shong.wang.certs
    INFO  XMLSecTool - Reading XML document from file 'AdmissionTicket.canonicalized.xml'
    INFO  XMLSecTool - XML document parsed and is well-formed.
    INFO  XMLSecTool - XML document successfully signed
    INFO  XMLSecTool - XML document written to file /home/weiwu/IdeaProjects/TokenScript/examples/ticket/AdmissionTicket.tsml
    # removing the canonicalized created for validation
    rm AdmissionTicket.canonicalized.xml

And now you get a signed TokenScript AdmissionTicket.tsml which is ready to be published.

## I don't use make

If your system doesn't support make, but you did follow the instruction to install xmlsectool, then you can use a command to sign the TokenScript:

   $ cd examples/ticket
   $ /opt/xmlsectool-2.0.0/xmlsectool.sh --sign --keyInfoKeyName 'Shong Wang' --digest SHA-256 --signatureAlgorithm http://www.w3.org/2001/04/xmldsig-more#rsa-sha256 --inFile AdmissionTicket.xml --outFile AdmissionTicket.tsml --key ssl/shong.wang.key --certificate ssl/shong.wang.certs

Unfortunately this command doesn't validate the XML file for you. The examples attached are pre-validated.

The command should produce a file `AdmissionTicket.tsml` which is ready to go.


# Publishing your TokenScript

A signed TokenScript is published by including its reference in the DAPP website that uses the token, similiar to how CSS files are refered. The detail steps for doing so will be provided in the coming weeks in April 2019.


