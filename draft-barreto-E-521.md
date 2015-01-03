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
For details on the relation see {{TwistedEdwards}}.

Each element x of GF(p) has a unique little-endian representation
as 66 bytes x\[0\] ... x\[65\], such that 
x\[0\] + 256 * x\[1\] + 256^2 * x\[2\] + ... + 256^65 * x\[65\] 
is congruent to x modulo p, and x\[65\] is minimal, i.e. only
the least significant bit of x\[65\] may be nonzero. 
Implementations MUST only produce field elements in this form.


# More drafty stuff
The rest consists of notes on what I would like to see here. 

# Shared secret computation

I think that using the basepoint (x,12) on E-521 as on safecurves
is good, I don't want to mess up people's implementations if they
choose to move between curves, so the point should be compatible.
This means that the Montgomery x is 13/11.

Use trick from Mike/Robert Ransom to work projectively with A, so that
we don't need to have a small A. In the end we work with (A-2)/4,
so with (2*(a+d)/(a-d) -2)/4 = d/(a-d)

Should we call this AN/AD and use
AN = -376014
AD = (1 + 376014)

Then (mostly copied from the Curve25519 draft) 

~~~~~~~~~~
    x_1 = x
    x_2 = 0
    z_2 = 1
    x_3 = x
    z_3 = 1
    For t = 520 down to 0:
        // Conditional swap; see text below.
        (x_2, x_3) = cswap (s_t, x_2, x_3)
        (z_2, z_3) = cswap (s_t, z_2, z_3)
        A = x_2 + z_2
        AA = A^2
        B = x_2 - z_2
        BB = B^2
        E = AA - BB
        C = x_3 + z_3
        D = x_3 - z_3
        DA = D * A
        CB = C * B
        x_3 = (DA + CB)^2
        z_3 = x_1 * (DA - CB)^2
	// choosing AP here to denote projective AA?
	AP = AA * AD
        x_2 = AP * BB
        z_2 = E * (AP + AN * E)
        // Conditional swap; see text below.
        (x_2, x_3) = cswap (s_t, x_2, x_3)
        (z_2, z_3) = cswap (s_t, z_2, z_3)
    Return x_2 * (z_2^(p - 1))
~~~~~~~~~~

In implementing this procedure, due to the existence of side-channels
in commodity hardware, it is important that the pattern of memory accesses
and jumps not depend on the values of any of the bits of s.
It is also important that the arithmetic used not leak information about 
the integers modulo p (such as having b * c distinguishable from c * c).

The cswap instruction SHOULD be implemented in constant time (independent 
of s_t) as follows:

cswap(s_t, x_2, x_3)
      dummy = s_t * (x_2 - x_3)
      x_2 = x_2 - dummy
      x_3 = x_3 + dummy
Return (x_2, x_3)

where s_t is 1 or 0. Alternatively, an implementation MAY use the
following:

      dummy = mask(s_t) AND (x_2 XOR x_3)
      x_2 = x_2 XOR dummy
      x_3 = x_3 XOR dummy

where mask(s_t) is the all-1 or all-0 word of the same length as x_2
and x_3, computed, e.g., as 
      mask(s_t) = 0 - s_t.
The latter version is often more efficient.

Then copy  the part on use in DH functions.

# E-521 for signatures

Are we all comfortable with recommending EdDSA for signatures?

I think we need to specify the window size for Edwards computations,
From the Curve41417 experience I think we can't take too large windows
because then loading all of them to make the table-lookup be constant
time is just too painful. Signed windows is less of a problem. What do
you think?

# Security considerations

The field is chosen as the field with around 512 bits that offers
the fastest field arithmetic. Reduction modulo p = 2^521 - 1 is 
particularly efficient because p is a Mersenne prime.
The value for d is the smallest in absolute value so that d
is not a square, #E and #E' (the twist of E) have both cofactor 4, 
and E satisfies the remaining criteria for a safe curve.
See also {{SafeCurves}}.

# other stuff
Example on Edwards, basepoint (x,12) as on safecurves page.


--- back


