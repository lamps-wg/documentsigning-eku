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


RFC 5280 specifies several extended key usages for X.509 certificates.
This document defines a general purpose document signing identifier 
for X.509 public key certificates' EKU value which restricts the usage of
the certificates for document signing.

--- middle

# Introduction

{{!RFC5280}} specifies several extended key usages for X.509
certificates. In addition, several purpose identifier had been added
{{?RFC7299}} as public Object Identifier (OID) under the IANA
repository. While usage of any extended key usage is bad practice
for publicly trusted certificates, there are no public and general
purpose identifier explicitly assigned for Document Signing
certificates. The current practice is to use id-kp-emailProtection,
id-kp-codeSigning or vendor defined OID for general document signing
purposes.

In circumstances where code signing and S/MIME certificates are also
widely used for document signing, the technical or policy changes that
are made to code signing and S/MIME certificates may cause unexpected
behaviors or have an adverse impact such as decreased cryptographic
agility on the document signing ecosystem and vice versa.

There is no issue if the vendor defined OIDs are used in a PKI (or a
trust program) governed by the vendor. However, if the OID is used
outside of the vendor governance, the usage can easily become out of
control (e.g. - When the end user encounters vendor defined OIDs, they
might want to ask that vendor about use of the certificate, however,
the vendor may not know about the particular use. - If the issuance of
the cert is not under the control of the OID owner, there is no way for
the OID owner to know what the impact will be if any change is made to
the OID in question, and it would restrict vendor's choice of OID
management. etc.).

Therefore, it is not favorable to use a vendor defined EKU for signing a
document that is not governed by the vendor.

This document defines a identifier of general Document Signing extended key purpose.

# Conventions and Definitions

{::boilerplate bcp14}

# Extended Key usage for DocumentSigning

This specification defines the KeyPurposeId id-kp-documentSigning.

As described in RFC5280, If the Extended Key Usage extension is present, 
then the certificate MUST only be used for one of the purposes indicated.
RFC5280 also describes that If multiple purposes are
indicated the application need not recognize all purposes indicated,
as long as the intended purpose is present.

Document Signing applications May require that the extended key usage extension be present 
and that a id-kp-documentSigning be indicated in order for the certificate to be acceptable 
to that Document Signing application.

Term of "Document Sign" (or "Document Signing") in this document is digitally sign (or signing)  contents that
are consumed by humans. To be more precise, contents are intended to be
shown to human with printable or displayable form by means of services
or software, rather than processed by machines.

## Extended Key Usage Values for Document Signing {#ext}

{{RFC5280}} specifies the EKU X.509 certificate extension for use in the
Internet. The extension indicates one or more purposes for which the
certified public key is valid. The EKU extension can be used in
conjunction with the key usage extension, which indicates how the public
key in the certificate is used, in a more basic cryptographic way.

The EKU extension syntax is repeated here for convenience:

~~~
  ExtKeyUsageSyntax  ::=  SEQUENCE SIZE (1..MAX) OF KeyPurposeId

  KeyPurposeId  ::=  OBJECT IDENTIFIER
~~~


This specification defines the KeyPurposeId id-kp-documentSigning.
Inclusion of this KeyPurposeId in a certificate indicates that the use
of any Subject names in the certificate is restricted to use by a
document signing service or a software (along with any usages allowed
by other EKU values).

~~~
  id-kp  OBJECT IDENTIFIER  ::=
      { iso(1) identified-organization(3) dod(6) internet(1)
        security(5) mechanisms(5) pkix(7) 3 }

  id-kp-documentSigning  OBJECT IDENTIFIER  ::=  { id-kp XX }
~~~

# Using the Document Signing EKU in a Certificate


{{?RFC8358}} specifies the conventions for digital signatures on
Internet-Drafts. This is one of the intended use cases for the general
document signing purpose identifier described in this document. {{RFC8358}} uses CMS
to digitally sign a wide array of files such as ASCII, PDF, EPUB, HTML
etc. Currently, there are no specification regarding EKU for
certificates signing those files except those which are defined by the
software vendor.

The signed contents of Internet-Drafts are primarily intended to be
consumed by people. To be more precise, contents are intended to be shown
to a person in a printable or displayable form by means of services or
software, rather than processed by machines. To validate the digital
signature on the contents that is intended to be consumed by people,
implementations MAY perform the steps below as a certificate validation.

The implementation MAY examine the Extended Key Usage value(s):
A Restriction on Extended Key Usage is derived and implemented from 
(or configured with) the policy to which the implementation conforms.

1. If there are no restrictions set for the relying party and the
relying party software, the certificate is acceptable.

2. If there are restrictions set for the replying party and relying
party software, proceed as following.

    Each Restriction on the EKUs can be “Excluded EKU” or “Permitted EKU”
and handled.

    The procedure is intended to permit or prohibit presence of 
a certain purpose identifier in EKU value or complete absence of EKU extension. 
It is outside the scope of this document, but the relying party 
can permit or exclude combinations of purpose identifiers. 
A consideration on prohibiting combination of purpose identifiers is described in
the security consideration section of this document.

    Excluded EKUs procedure:

    : “Excluded EKU” is an EKU or some purpose identifier, 
which the relying party or the relying party software prohibits. 
Examples of "Excluded EKU" are, presence of
anyExtendedKeyUsage or complete absence of EKU extension on a certificate.
If a purpose identifier in of the certificate meets the conditions set by the “Excluded EKU”
restriction, the relying party or the relying party software rejects the
certificate.

    Permitted EKU procedure:

    : “Permitted EKU” is a purpose identifier which the relying party or the relying party
software accepts. Examples of “Permitted EKU” are, presence of this
general document signing purpose identifier and/or protocol specific document
signing-type purpose identifiers. If an EKU of the certificate meets the condition set
by a “Permitted EKU” restriction, the certificate is acceptable.
Otherwise, relying party or the relying party software rejects the
certificate.

When a single software has capability to process various data formats,
the software may choose to make the excluded and permitted decisions
separately in accordance with the format it is handling (e.g. text, pdf,
etc).

# Implications for a Certification Authority

The procedures and practices employed by a certification authority MUST
ensure that the correct values for the EKU extension are inserted in
each certificate that is issued. Unless certificates are governed by a
vendor specific PKI (or trust program), certificates that indicate usage
for document signing MAY include the id-kp-documentSigning in EKU
extension. This does not encompass the mandatory usage of the
id-kp-documentSigning in conjunction with the vendor specific EKU.
However, this does not restrict the CA from including multiple purpose
identifiers related to document signing.

# Security Considerations


The usage of id-kp-documentSigning intends to prevent
id-kp-emailProtection from being used for purposes other than email and
id-kp-codeSigning used to sign objects other than binary codes. 
This purpose identifier
does not introduce new security risks but instead reduces existing
security risks by providing means to separate other purposes used for
communication protocols namely, TLS or S/MIME etc. in order to minimize
the risk of cross protocol attacks.

To reduce the risk of specific cross protocol attacks, the relying party
or relying party software may additionaly prohibit use of specific
combination of purpose identifiers on EKU value.

While a specific protocol or signing scheme may choose to come up with
their own purpose identifier(s), some may not have significant motive or resource to set
up and manage thier own purpose identifier. This general document signing purpose identifier may be
used as a stop gap for those that intend to set up their own EKU or
those who do not intend to set up an EKU but still would like to
distinguish from other usage.

Introduction of this id-kp-documentSigning in EKU value does not introduce
any new security or privacy concerns.

# IANA Considerations

This document requests that IANA make two assignments. One for the
id-kp-documentSigning object identifier (OID), as defined in {{ext}},
for the EKU from the "SMI Security for PKIX Extended Key Purpose"
(1.3.6.1.5.5.7.3) registry.  Another for the id-mod-docsign-eku, as
defined in Appendix A, for the ASN.1 module {{X.680}} from the in the
"SMI Security for PKIX Module Identifier" (1.3.6.1.5.5.7.0) registry.
No further action is necessary by IANA.

--- back

# Appendix A. ASN.1 Module
{:numbered="false"}

The following ASN.1 module provides the complete definition of the
Document Signing EKU.

~~~
  DocSignEKU { iso(1) identified-organization(3) dod(6) internet(1)
    security(5) mechanisms(5) pkix(7) id-mod(0)
    id-mod-docsign-eku(TBD1) }

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
