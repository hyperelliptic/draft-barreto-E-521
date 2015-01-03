---
title: E-521
abbrev: E-521
docname: draft-barreto-E-521-latest
date: 2015-01-01
category: info

ipr: trust200902 
area: Security
workgroup: CFRG
keyword: Internet-Draft

stand_alone: yes
pi:
   toc: no
   sortrefs: yes
   symrefs: yes

author:
-
    ins: P. S. L. M. Barreto  
    name: Paulo Sergio Licciardi Messeder Barreto 
    organization: University of São Paulo 
    email: pbarreto@larc.usp.br
-
    ins: M. Hamburg
    name: Michael Hamburg
    org: XXX
    email: mike@shiftleft.org
-
    ins: T. Lange
    name: Tanja Lange
    org: Technische Universiteit Eindhoven
    email: tanja@hyperelliptic.org 

normative:
    RFC2119:

informative:
    E521: 
        target: http://eprint.iacr.org/2013/647
        title: A note on high-security general-purpose elliptic curves
        author:
        -
            name: Diego F. Aranha
            ins: D.F. Aranha
        -
            name: Paulo S. L. M. Barreto
            ins: P.S.L.M. Barreto  
        - 
            name: Geovandro C. C. F. Pereira
            ins: G.C.C.F. Pereira
        -  
            name: Jefferson Ricardini 
            ins: J. Ricardini
        date: 2013-11-04

    SafeCurves:
        target: http://safecurves.cr.yp.to/
        title: SafeCurves -- choosing safe curves for elliptic-curve cryptography
        author:
        -
            name: Daniel J. Bernstein
            ins: D.J. Bernstein
        -
            name: Tanja Lange
            ins: T. Lange
        date: 2013

    TwistedEdwards:
        target: http://eprint.iacr.org/2008/013
        title: Twisted Edwards Curves
        author:
        -
            name:  Daniel J. Bernstein
            ins: D.J. Bernstein
        -
            name: Peter Birkner
            ins: P. Birkner
        -
            name: Marc Joye
            ins: M. Joye
        -
            name: Tanja Lange
            ins: T. Lange    
        -
            name: Christiane Peters
            ins: C. Peters
        date: 2008

    GrangerScott:
        target: http://eprint.iacr.org/2014/852
        title: Faster ECC over $\mathbb{F}_{2^{521}-1}$
        author:
        -
            name: Robert Granger
            ins: R. Granger
        -
            name: Michael Scott
            ins: M. Scott
        date: 2014-10-17

    BarretoAranha:
        target: http://www.larc.usp.br/~pbarreto/manifesto-curvas.pdf
        title: Sobre os padrões criptográficos de curvas elípticas adotados pela ICP-Brasil
        author: 
        -
            name: Paulo S. L. M. Barreto
            ins: P.S.L.M. Barreto
        -
            name: Diego F. Aranha
            ins: D.F. Aranha
        date: 2014-07-17      

--- abstract

This document describes the curve E-521 and its use for internet protocols.
It specifies the wire format for ECDH (Elliptic-curve Diffie-Hellman) 
key-agreement as well as the key agreement computations. It also specifies
the wire format and computations for an elliptic-curve signature scheme.

--- middle

# Introduction

This document describes the curve E-521 and its use for internet protocols.
It specifies the wire format for ECDH (Elliptic-curve Diffie-Hellman) 
key-agreement as well as the key agreement computations. It also specifies
the wire format and computations for an elliptic-curve signature scheme.

This provides a stable reference for the curve E-521. This curve was 
found independently by three teams:
        Michael Hamburg
        Daniel J. Bernstein and Tanja Lange
        Diego F. Aranha and Paulo S. L. M. Barreto and Geovandro C. C. F. Pereira and Jefferson E. Ricardini, see {{E521}}
in fall 2013 {{SafeCurves}}.

This curve has been proposed for use in Brazilian IT systems (see
{{BarretoAranha}}) and benchmarking results are available {{GrangerScott}}.

## Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in {{RFC2119}}.

# Notation and Definitions

The following notation and definitions are used in this document
(notation is to the left of the ":"):

A: A value used in an elliptic-curve equation.

B: A value used in an elliptic-curve equation.

d: A value used in an elliptic-curve equation.

E: An elliptic-curve.

p: A prime.

GF(p): The field with p elements.

_#: Subscript notation, where # is a number or letter.

=: Assignment.

^: Exponentiation.

+, -, *, /: Addition, subtraction, multiplication, and division, respectively.

Note that all operations are performed modulo p.

# The E-521 curve 

Let p = 2^521 - 1. Let E-521 be the elliptic curve with equation
in Edwards form
x^2 + y^2 = 1 - 376014 * x^2 * y^2 over GF(p).
The Montgomery form of E-521 is given by 
B * y^2 = x^3 + A * x^2 + x over GF(p), where
A = 2 * (1 - 376014)/(1 + 376014) and B = 4/(1 + 376014).
The map from the Edwards curve takes a point (x,y) to the point
((1 + y)/(1 − y),(1 + y)/(1 − y) * x).
The map from the Montgomery curve takes a point (x,y) to the point
(x/y, (x − 1)/(x + 1)).

Each element x of GF(p) has a unique little-endian representation
as 66 bytes x\[0\] ... x\[65\], such that 
x\[0\] + 256 * x\[1\] + 256^2 * x\[2\] + ... + 256^65 * x\[65\] 
is congruent to x modulo p, and x\[65\] is minimal. 
Implementations MUST only produce field elements in this form.


# Security considerations

The field is chosen as the field with around 512 bits that offers
the fastest field arithmetic. Reduction modulo p = 2^521 - 1 is 
particularly efficient because p is a Mersenne prime.
The value for d is the smallest in absolute value so that d
is not a square, #E and #E' (the twist of E) have both cofactor 4, 
and E satisfies the remaining criteria for a safe curve.
See also {{SafeCurves}}.

--- back


