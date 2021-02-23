# GS1 Digital Link URI Verifier for EPCIS
### Purpose of this Jupyter Notebook: 
* Basis for discussion in EPCIS/CBV 2.0 MSWG
* Potential starting point of an open source artefact to help in applying constrained GS1 Digital Link URIs for populating EPCIS events 

### Functionality:
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

### Status: DRAFT (for discussion purposes)
