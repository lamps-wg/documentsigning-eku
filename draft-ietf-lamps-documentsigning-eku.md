---
title: General Purpose Extended Key Usage (EKU) for Document Signing X.509 Certificates
abbrev: EKU for Document Signing
category: std

docname: draft-ietf-lamps-documentsigning-eku-latest
ipr: trust200902
keyword: Internet-Draft
area: SEC
workgroup: LAMPS Working Group
venue:
  group: "Limited Additional Mechanisms for PKIX and SMIME (LAMPS)"
  type: "Working Group"
  mail: "spasm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: lamps-wg/documentsigning-eku

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: T. Ito
    name: Tadahiko Ito
    organization: SECOM CO., LTD.
    email: tadahiko.ito.public@gmail.com
 -
    ins: T. Okubo
    name: Tomofumi Okubo
    organization: DigiCert, Inc.
    email: tomofumi.okubo+ietf@gmail.com
 -
    ins: S. Turner
    name: Sean Turner
    organization: sn3rd
    email: sean@sn3rd.com

normative:
  X.680:
      title: "Information technology - Abstract Syntax Notation One (ASN.1): Specification of basic notation"
      date: November 2015
      author:
        org: ITU-T
      seriesinfo:
        ISO/IEC: 8824-1:2015

--- abstract

{{!RFC5280}} specifies several extended key usages for X.509 certificates. This document defines a general purpose document signing extended key usage for X.509 public key certificates which restricts the usage of the certificates for document signing.

--- middle

# Introduction

{{!RFC5280}} specifies several extended key usages for X.509 certificates. In addition,
several extended key usage had been added{{?RFC7299}} as public OID under the IANA repository.
While usage of any extended key usage is bad practice for publicly trusted certificates,
there are no public and general extended key usage explicitly assigned for Document Signing certificates.
The current practice is to use id-kp-emailProtection, id-kp-codeSigning or vendor defined Object ID
for general document signing purposes.

In circumstances where code signing and S/MIME certificates are also widely used for document signing,
the technical or policy changes that are made to code signing and S/MIME certificates may cause
unexpected behaviors or have an adverse impact such as
decreased cryptographic agility on the document signing ecosystem and vice versa.

There is no issue if the vendor defined OIDs are used in a PKI (or a trust program) governed by the vendor.
However, if the OID is used outside of the vendor governance, the usage can easily become out of control
(e.g. - When the end user encounters vendor defined OIDs, they might want to ask that vendor about use of the certificate, however, the vendor may not know about the particular use. - If the issuance of the cert is not under the control of the OID owner, there is no way for the OID owner to know what the impact will be if any change is made to the OID in question, and it would restrict vendor's choice of OID management. etc.).

Therefore, it is not favorable to use a vendor defined EKU for signing a document that is not governed by the vendor.

This document defines a general Document Signing extended key usage.

# Conventions and Definitions

{::boilerplate bcp14}

# Extended Key usage for DocumentSigning

This specification defines the KeyPurposeId id-kp-documentSigning.
Inclusion of this KeyPurposeId in a certificate indicates that the
use of any Subject names in the certificate is restricted to use by a document signing.

Term of "Document Sign" in this document is digitally sign contents that are consumed by humans.
To be more precise,
contents are intended to be shown to human with printable or displayable form by means of services or software,
rather than processed by machines.

## Extended Key Usage Values for Document Signing {#ext}

{{RFC5280}} specifies the EKU X.509 certificate extension for use in the Internet.  The extension indicates one or more purposes for which the certified public key is valid.  The EKU extension can be used in conjunction with the key usage extension, which indicates how the public key in the certificate is used, in a more basic cryptographic way.

The EKU extension syntax is repeated here for convenience:

~~~
    ExtKeyUsageSyntax  ::=  SEQUENCE SIZE (1..MAX) OF KeyPurposeId
    KeyPurposeId  ::=  OBJECT IDENTIFIER
~~~

This specification defines the KeyPurposeId id-kp-documentSigning. Inclusion of this KeyPurposeId in a certificate indicates that the use of any Subject names in the certificate is restricted to use by a document signing service or a software (along with any usages allowed by other EKU values).

~~~
    id-kp  OBJECT IDENTIFIER  ::=
        { iso(1) identified-organization(3) dod(6) internet(1)
          security(5) mechanisms(5) pkix(7) 3 }
    id-kp-documentSigning  OBJECT IDENTIFIER  ::=  { id-kp XX }
~~~

# Using the Document Signing EKU in a Certificate

{{?RFC8358}} specifies the conventions for digital signatures on Internet-Drafts. This is one of the intended use cases for the general document signing EKU described in this document. {{RFC8358}} uses CMS to digitally sign a wide array of files such as ASCII, PDF, EPUB, HTML etc. Currently, there are no specification regarding EKU for certificates signing those files except those which are defined by the software vendor.

The signed contents of Internet-Drafts are primarily intended to be consumed by human. To be more precise, contents are intended to be shown to human in a printable or displayable form by means of services or software, rather than processed by machines. To validate the digital signature which is signed to contents intended to be consumed by human, implementations MAY perform the steps below as a certificate validation:

The implementation MAY examine the Extended Key Usage value(s):

1. If there are no restrictions set for the relying party and the relying party software, the certificate is acceptable.

2. If there are restrictions set for the replying party and relying party software, proceed as following.

Each Restriction on the EKUs can be “Excluded EKU” or “Permitted EKU” and handled.

The procedure is intended to permit or prohibit presence of a certain EKU or complete absence of EKUs. It is outside the scope of this document, but the relying party can permit or prohibit conbinations of EKU. A consideration on prohibiting combination of EKUs is described in the security consideration section of this document.

2.1. Excluded EKUs procedure
“Excluded EKU” is an EKU which the relying party or the relying party software prohibits. Examples of "Excluded EKU" are, presence of anyEKU or complete absence of EKU extension on a certificate. If an EKU of the certificate meets the conditions set by the “Excluded EKU” restriction, the relying party or the relying party software rejects the certificate.

2.2. Permitted EKU procedure
“Permitted EKU” is an EKU which the relying party or the relying party software accepts. Examples of “Permitted EKU” are, presence of this general document signing EKU and/or protocol specific document signing-type EKUs. If an EKU of the certificate meets the condition set by a “Permitted EKU” restriction, the certificate is acceptable. Otherwise, relying party or the relying party software rejects the certificate.

When a single software has capability to process various data formats, the software may choose to make the excluded and permitted decisions separately in accordance with the format it is handling (e.g. text, pdf, etc).

# Implications for a Certification Authority
The procedures and practices employed by a certification authority MUST ensure that the correct values for the EKU extension are inserted in each certificate that is issued.
Unless certificates are governed by a vendor specific PKI (or trust program), certificates that indicate usage for document signing MAY include the id-kp-documentSigning EKU extension. This does not encompass the mandatory usage of the id-kp-documentSigning EKU in conjunction with the vendor specific EKU. However, this does not restrict the CA from including multiple EKUs related to document signing.

# Security Considerations
The usage of id-kp-documentSigning EKU intends to prevent id-kp-emailProtection from being used for none-email purposes and id-kp-codeSigning used to sign objects other than binary codes. This EKU does not introduce new security risks but instead reduces existing security risks by providing means to separate other EKUs used for communication protocols namely, TLS or S/MIME etc. in order to minimize the risk of cross protocol attacks.

To reduce the risk of specific cross protocol attacks, the relying party or relying party software may additionaly prohibit use of specific combination of EKUs.

While a specific protocol or signing scheme may choose to come up with their own EKU, some may not have significant motive or resource to set up and manage thier own EKU. This general document signing EKU may be used as a stop gap for those that intend to set up their own EKU or those who do not intend to set up an EKU but still would like to distinguish from other usage.

Introduction of this id-kp-documentSigning EKU value does not introduce any new security or privacy concerns.

# IANA Considerations

This document requests that IANA make two assignments. One for the id-kp-documentSigning object identifier (OID), as defined in {{ext}}, for the EKU from the "SMI Security for PKIX Extended Key Purpose" (1.3.6.1.5.5.7.3) registry.  Another for the id-mod-docsign-eku, as defined in Appendix A, for the ASN.1 module {{X.680}} from the in the "SMI Security for PKIX Module Identifier" (1.3.6.1.5.5.7.0) registry.  No further action is necessary by IANA.

--- back

# Appendix A. ASN.1 Module
{:numbered="false"}

The following ASN.1 module provides the complete definition of the
Document Signing EKU.

~~~
   DocSignEKU { iso(1) identified-organization(3) dod(6) internet(1)
   security(5) mechanisms(5) pkix(7) id-mod(0) id-mod-docsign-eku(TBD1) }

   DEFINITIONS EXPLICIT TAGS ::=

   BEGIN

   -- EXPORTS ALL --

   -- IMPORTS NOTHING --

   -- OID Arc --

   id-kp  OBJECT IDENTIFIER  ::= {
     iso(1) identified-organization(3) dod(6) internet(1)
     security(5) mechanisms(5) pkix(7) kp(3) }

   -- Document Signing Extended Key Usage --

   id-kp-documentSigning OBJECT IDENTIFIER ::= { id-kp TBD2 }

   END
~~~

# Acknowledgments
{:numbered="false"}

We would like to thank Russ Housley for verifying the ASN.1 module.
