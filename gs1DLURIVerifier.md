# GS1 Digital Link URI Verifier for EPCIS
### Purpose of this notebook: 
* Basis for discussion in EPCIS/CBV 2.0 MSWG
* Potential starting point of an open source artefact to help in applying constrained GS1 Digital Link URIs for populating EPCIS events 

### Functionality:
* Checks whether a list of GS1 DL URIs conforms to the constrained syntax as defined in CBV 2.0
* Specifically:
 * checks if GS1 DL URIs only comprise the primary identifier at the lowest, i.e. most precise level so that they correspond to the EPC URI/EPC Class URI schemes as defined in the EPC Tag Data Standard
 * accepts any user domain/sub-domain (i.e. not just canonical GS1 DL URIs)
 * checks that if a GS1 DL URI includes a GTIN, the latter is represented in its 14-digit format so that it is consistent with the definition of GS1 Application Identifier '01' 
* Input: list of Digital Link URIs (as they are intended to populate the epcList/quantityList)
* Output: list of Boolean values, in identical order as the input list, either 'True' (if conformant) or 'false' (if not) for each list element
* Supported instance-level keys/compound keys: 
 * SSCC 
 * GTIN + Serial (SGTIN)
 * UPUI
 * GDTI with serial
 * GCN with serial
 * GINC
 * GSIN 
* Supported class-level keys/compound keys: 
 * GTIN
 * GTIN + Lot (LGTIN)
 * GDTI without serial
 * GCN without serial
* Supported location/party keys:
 * GLN with extension
 * GLN without extension
 * Party GLN

### Status: DRAFT (for discussion purposes)


```python
import re
```

The following list contains instance-level ID candidates for populating the epcList (and equivalent ID lists depending on the EPCIS event type) with constraint GS1 Digital Link URIs. 


```python
dlURIEPCList = [
    'https://id.gs1.org/00/340123451111111111',
    'https://example.com/01/04150567890128/21/987654',
    'https://example.com/01/04150567890128/235/UPUISERIALNUMBER987654324151',
    'https://id.gs1.org/253/4602443000331XYZ',
    'https://id.gs1.org/255/0811625999996554433',
    'https://id.gs1.org/401/401234522233ABcd',
    'https://id.gs1.org/402/40123452223334442',

    'https://id.gs1.org/414/4226350800008',
    'https://example.co.uk/417/4226350800008',
    'https://id.gs1.org/414/4280000000002/254/12',
    'https://id.gs1.org/8003/03870585000552987',
    'https://id.gs1.org/8004/0180451111ABC987',
    'https://id.gs1.org/8018/385888700111111111',
    'https://id.gs1.org/8017/440018922222222226', 
    'https://id.gs1.org/8010/0628165987/8011/9876',
    'https://id.gs1.org/255/0811625999996554433',
    'https://id.gs1.org/sscc/340123451111111111',
    'https://id.gs1.org/00/340123451111111111/',
    'https://www.someURL.info/01/02/03/04',
    'https://id.gs1.org/01/9780345418913/22/344',
    'https://id.gs1.org/01/9780345418913/22/344/10/1223/21/765tz',
    'https://id.gs1.org/8006/123456789101100190/22/ABCD',
    'https://example.org/01/9780345418913/21/765tz?11=221109',
    'https://example.org/01/9780345418913/21/765tz?abc=211121',
    'http://example.org/123/4012345ABC987/456/4711',
    'http://id.gs1.org/01/9780345418913/99/utfgf',
    'https://example.com/01/04150567890128/235/UPUISERIALNUMBER9876543241516161',
    'https://example.com/01/04150567890128/21/',
    'https://id.gs1.org/253/4602443000331',
    '(01)04012345123456(21)987',
    '01/04012345123456/21/987',
    'https://example.com/01/04150567890128/ 21/',
    'ftp://example.com/01/04150567890128/21/123',
    'https://id.gs1.org/01/04012345123456'
    

]
```

With the following regular expression, organisations can verify that an epcList (and equivalent ID lists depending on the EPCIS event type) are valid constraint GS1 Digital Link URIs. 
The above list is passed to a re.match() function and returns a list with a Boolean value for each list element. If the return value is 'False', the ID candidate is not a valid instance-level, constrained GS1 DL URI. 


```python
validatedEPCList = []
for j in dlURIEPCList:
    mO = re.match(r'''https?:(//((([^/?#]*)@)?([^/?#:]*)(:([^/?#]*))?))?([^?#]*)/( # domain/sub-domain
                    (00/\d{18}$)| # SSCC
                    # Allowed chars in serial reference as of GS1 DL standard: " / % / - / . / 0-9 / A-Z / _ / a-z
                    (01/\d{14}/21/([\x22\x25\x2d\x2E\x30-\x39\x41-\x5A\x5F\x61-\x7A]{1,20})$)| # SGTIN
                    (01/\d{14}/235/([\x22\x25\x2d\x2E\x30-\x39\x41-\x5A\x5F\x61-\x7A]{1,28})$)| # UPUI
                    (253/\d{13}([\x22\x25\x2d\x2E\x30-\x39\x41-\x5A\x5F\x61-\x7A]{1,17})$)| # GDTI with serial
                    (255/\d{13}(\d{1,12})$)| # GCN with serial
                    (401/([\x22\x25\x2d\x2E\x30-\x39\x41-\x5A\x5F\x61-\x7A]{5,30})$)| # GINC 
                    # Lower quantifier of GINC TBD. Rationale for '5': GCP >= 4 Digits 
                    # May adjust https://www.gs1.org/standards/barcodes/application-identifiers/401?lang=en
                    (402/\d{17}$) # GSIN 
                    # TO BE COMPLETED   
                    ) ''', j, re.VERBOSE)
    if mO:
        validatedEPCList.append('True')
    else:
        validatedEPCList.append('False')
print (validatedEPCList)
```

    ['True', 'True', 'True', 'True', 'True', 'True', 'True', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'True', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False', 'False']


The following list contains class-level ID candidates for populating the quantityList (and equivalent ID lists depending on the EPCIS event type) with constraint GS1 Digital Link URIs.


```python
dlURIQtyList = [
    'https://id.gs1.org/01/04012345123456',
    'https://gs1.dl.uri.with.a.lot.of.sub.domains.test.example.org/01/09780345418913/10/1223',
    'https://id.firma.info/01/04012345123456/10/ABC',
    'http://www.id.example.de/01/04012345123456',
    'https://id.gs1.org/253/4602443000331',
    'https://id.gs1.org/255/0811625999996',
    
    'https://id.example.com/01/4012345123456',
    'http://us-company-with-UPC.com/01/001122334455/ser/GHB',
    'https://id.gs1.org/01/12345670',
    'https://id.gs1.org/01/9780345418913/10/Lot1/21/Ser2',
    'https://id.gs1.org/01/9780345418913/22/344/10/1223/21/765tz',
    'https://id.gs1.org/01/9780345418913/10/Lot666/21/Ser888',
]
```

With the following regular expression, organisations can verify that a quantityList (and equivalent ID lists depending on the EPCIS event type) are valid constraint GS1 Digital Link URIs. The above list is passed to a re.match() function and returns a list with a Booloean value for each list element. If the return value is 'False', the ID candidate is not a valid class-level, constrained GS1 DL URI.


```python
validatedQtyList = []
for j in dlURIQtyList:
    mO = re.match(r'''https?:(//((([^/?#]*)@)?([^/?#:]*)(:([^/?#]*))?))?([^?#]*)/( # domain/sub-domain
                    (01/\d{14}$)| # GTIN without serial
                    (01/\d{14}/10/([\x22\x25\x2d\x2E\x30-\x39\x41-\x5A\x5F\x61-\x7A]{1,20})$)| # LGTIN
                    (253/\d{13}$)| # GDTI without serial
                    (255/\d{13}$) # GCN without serial
                    # TO BE COMPLETED   
                    ) ''', j, re.VERBOSE)
    if mO:
        validatedQtyList.append('True')
    else:
        validatedQtyList.append('False')
print (validatedQtyList)
```

    ['True', 'True', 'True', 'True', 'True', 'True', 'False', 'False', 'False', 'False', 'False', 'False']


The following list contains ID candidates for populating the readPoint/bizLocation/sourceList/destinationList with constraint GS1 Digital Link URIs.


```python
dlURILoPaList = [
    'https://id.gs1.org/414/4280000000002/254/12',
    'https://id.gs1.org/414/4226350800008',
    'https://id.gs1.org/417/4012345999990',
    
    'https://id.gs1.org/414/4226350800008/',
    'https://id.gs1.org/417/401234599999',
    'https://id.gs1.org/gln/4226350800008',
    'https://id.gs1.org/414/4280000000002/glnx/12',
    'https://id.gs1.org/party/4012345999990',
]
```


```python
validatedLoPaList = []
for j in dlURILoPaList:
    mO = re.match(r'''https?:(//((([^/?#]*)@)?([^/?#:]*)(:([^/?#]*))?))?([^?#]*)/( # domain/sub-domain
                    (414/\d{13}$)| # GLN without extension
                    (414/\d{13}/254/([\x22\x25\x2d\x2E\x30-\x39\x41-\x5A\x5F\x61-\x7A]{1,20})$)|# GLN with extension
                    (417/\d{13}$) # Party GLN
                    ) ''', j, re.VERBOSE)
    if mO:
        validatedLoPaList.append('True')
    else:
        validatedLoPaList.append('False')
print (validatedLoPaList)
```

    ['True', 'True', 'True', 'False', 'False', 'False', 'False', 'False']


### Invalid examples (i.e. those returning 'False') cover the following cases:
* GTIN not formatted as GTIN-14 (i.e. corresponding to definition of GS1 Application Identifier (AI) '01')
* Applying short names instead of AI equivalents
* GTIN-based GS1 DL URI comprising consumer product variant (AI '22') 
* GTIN-based GS1 DL URI comprising lot (AI 10) AND serial (AI 21) as key qualifiers (note: only one is allowed at a time)
* Containing characters that need to be percent-encoded 
* Ending with a trailing slash 
* AI equivalents in the query string
* Custom extensions in the query string
* Comprising non-existent AIs
* Data payload (e.g. serial number) exceeding character limit as defined per GS1 General Specifications
* Empty fields (e.g. batch number or serial number without content)
* Lacking a domain name
* Containing space characters
* Class-level (instead of instance-level) ID and vice versa
