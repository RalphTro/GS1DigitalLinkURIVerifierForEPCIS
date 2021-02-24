# GS1 Digital Link URI Verifier for EPCIS

### Purpose of this Jupyter Notebook
* Basis for discussion in EPCIS/CBV 2.0 MSWG
* Potential starting point of an open source artefact to help in applying constrained GS1 Digital Link URIs for populating EPCIS events 

### Functionality
* Checks whether a list of GS1 DL URIs conforms to the constrained syntax as defined in CBV 2.0
* Specifically:
 * checks if GS1 DL URIs only comprise the primary identifier at the lowest, i.e. most precise level so that they correspond to the EPC URI/EPC Class URI schemes as defined in the EPC Tag Data Standard
 * accepts any user domain/sub-domain (i.e. not just canonical GS1 DL URIs)
 * checks that if a GS1 DL URI includes a GTIN, the latter is represented in its 14-digit format so that it is consistent with the definition of GS1 Application Identifier '01' 
* Input: list of Digital Link URIs (as they are intended to populate the epcList/quantityList)
* Output: dictionary (JSON object) which contains, for all entries of the input list, key-value pairs with the contained GS1 DL URIs and a corresponding Boolean value - either 'True' (if conformant) or 'false' (if not)
* Supported instance-level keys/compound keys: 
  * SSCC 
  * GTIN + Serial (SGTIN)
  * UPUI
  * GDTI with serial
  * SGCN with serial
  * GINC
  * GSIN 
  * GRAI
  * GIAI
  * ITIP
  * CPI
  * GSRN
  * GSRNP
* Supported class-level keys/compound keys: 
  * GTIN
  * GTIN + Lot (LGTIN)
  * GDTI without serial
  * SGCN without serial
  * GRAI without serial 
  * CPI without serial
* Supported location/party keys:
  * GLN with extension
  * GLN without extension
  * Party GLN
* Cases covered through invalid examples (i.e. those returning 'False'):
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

### Status
DRAFT (for discussion purposes)

### Notes and references 
* For the normative Regular Expressions, please refer to the CBV 2.0 standard, available at [https://www.gs1.org/standards/epcis]. 
* Regular Expressions are not identical in all programming languages. In this context, this Jupyter Notebook [https://jupyter.org] is based on Python [https://www.python.org/] and makes use of some simplifications enabled by Python's regular expressions ('re') module [https://docs.python.org/3/library/re.html], e.g. RegEx annotations and raw string notation for better readability and comprehensibility.
* Further, it makes use of metacharacters ('\d' instead of '0-9' and '\w' instead of 'a-zA-Z0-9_') for the sake of brevity.

## License

<img alt="MIT" style="border-width:0" src="https://opensource.org/files/OSIApproved_1.png" width="150px;"/><br />

Copyright 2021 | Ralph Tröger <ralph.troeger@gs1.de>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
