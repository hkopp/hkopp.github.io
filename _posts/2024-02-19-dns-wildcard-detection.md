---
layout: post
category : lesson
tags : [hacking, programming]
---

# Introduction
This blog post concerns how to detect wildcard DNS records.
This is an important step, e.g., when enumeration subdomains using
brute force, which is a common part of bug bounty hunting and CTF
playing.

# Background

A wildcard DNS record is a DNS record that matches any subdomain.
An example is 
`*.example.com` that matches every subdomain that ends with
`.example.com`.

Wildcards in DNS only work as the leftmost wildcard label. This means
that `*.example.com` is valid. The resource records
`www.*.example.com` and `*www.example.com` are both invalid.
Further, wildcards can occur at any level except for some top level domain,
i.e. `*.www.example.com` is again valid.

A wildcard matches only if the resource record does not exist. This
means that even if there is a wildcard for `*.example.com`, we could
have a different DNS record for `www.example.com`, as that is not
matched by the wildcard.

There are multiple RFCs regarding DNS wildcards, such as [RFC 1034](https://datatracker.ietf.org/doc/html/rfc1034)
[RFC 4592](https://datatracker.ietf.org/doc/html/rfc4592).
There are additional clarifications in [RFC 1912](https://datatracker.ietf.org/doc/html/rfc1912).

Still, wildcard matching is unintuitive and badly defined. To quote
RFC 1912, "A common mistake is thinking that a wildcard MX for a zone
will apply to all hosts in the zone. A wildcard MX will apply only to
names in the zone which aren't listed in the DNS at all."
That means, if there is a wildcard MX for `*.example.com`, and an A record (but no MX
record) for `www.example.com`, the correct response (as per RFC 1034) to
an MX request for `www.example.com` is "no error, but no data". This is
in contrast to the expected response of the MX record
attached to `*.example.com`. 

Note that this is only according to the specification, and
implementations can differ wildly.

Additionally, funny things can happen if wildcard DNS records interact
with load balancers.
As an example, if there is a wildcard DNS entry `*.example.com`, then
`abc.example.com` and `def.example.com` could still
(deterministically) resolve to different IP addresses due to the
presence of a DNS load balancer.

# Wildcard Detection

When enumeration subdomains of a domain using tools such as gobuster,
dnsx, or similar, we are interested only in the *real* domains that
offer web services. This means we have to detect if we have a wildcard
match or a real domain.

## Detection Approach 1: Querying Nonexistent Subdomains
One approach that is often recommended on the Internet to detect the
presence of a wildcard DNS entry is to query for
`<somerandomstring>.example.com`. If the string is long enough, then
the probability that this subdomain exists is negligible.  If an A
record is returned, there probably exists a wildcard DNS record
`*.example.com`. The probability of an incorrect detection can be
decreased further when querying different random subdomains.

- *Advantage*: We can detect the presence of a DNS wildcard with arbitrary
  precision.

- *Disadvantage*: We cannot detect exceptions of the wildcards. Thus,
  if there is a resource record `*.example.com` and `www.example.com`,
  then we can not detect the presence of `www.example.com`.

## Detection Approach 2: Querying the Wildcard Directly
An even more reliable approach for detecting the presence of a
wildcard DNS entry is to query directly for `*.example.com`. This can
be achieved for example using `dig '*.example.com'` If this record
exists, then we know for sure that the wildcard DNS resource record
exists.

- *Advantage*: We are certain to detect the presence of a DNS
  wildcard.

- *Disadvantage*: As above we cannot detect exceptions to the
  wildcards. Thus, if there is a resource record `*.example.com` and
  `www.example.com`, then we can not detect the presence of
  `www.example.com`.


## Detection Approach 3: Counting Records
In order to additionally detect exceptions to the wildcards, we could
count the different IPs that are returned. Then we reject those subdomains
corresponding to frequent IPs, where frequent means "larger than some
threshold".

Suppose we perform DNS queries for

- `api.example.com`
- `confluence.example.com`
- `en.example.com`
- `ftp.example.com`
- `gitlab.example.com`
- `k8s.example.com`
- `wiki.example.com`
- `www.example.com`

Further, suppose that all these queries return the IP address
`1.2.3.4`, except for `www.example.com` that returns `4.3.2.1`.  Then
we could heuristically reason that there is one DNS record for
`www.example.com`, and one for `*.example.com`.

- *Advantage*: With this detection approach, we can heuristically
  detect the wildcards, and some of the exceptions to the wildcards,
  that we have in our wordlist of queries.

- *Disadvantage*: The disadvantage to this approach is that it only
  works heuristically, i.e., it is not reliable. It is possible that
  *we detect wildcard records that do not exist*. As an example, the
  resource records for the subdomains that resolve to `1.2.3.4` could
  all exist individually. This is often the case if they are hosted at
  some cloud provider, or in the case of the same shop in different
  languages and thus with different top level domains. However, with
  this method they are deemed as being wildcards, even though they are
  not.
  Further, *we do not detect all wildcard records*. As an example,
  consider that there is a wildcard domain that is combined with a DNS
  load balancer. In that case the DNS server returns multiple IPs for
  the same wildcard DNS entry. Our approach would (depending on the
  threshold) detect that these domains do not belong to wildcard
  records, even though they do.

## Detection Approach 4: Counting Records and Querying Wildcards
This approach combines the two approaches explained above.
First, we query for `*.example.com`. If this record does not exist,
then we know that there is no such wildcard DNS resource record and
we can continue with out wordlist. Even if all queried domains resolve
to the same IP address, we know that this is not due a wildcard resource
record. It could for example be due to a shared hosting environment.

If the wildcard record does exist, then we know that we have to deal
with a wildcard DNS record. However, regarding the detection of
exceptions, we are none the wiser and still continue with the
wordlist. Frequent IPs are assumed to have been matched by the
wildcard DNS record. However, this assumption could still be wrong.
Infrequent IPs could still have been matched by the wildcard and be
the result of DNS loadbalancing.

- *Advantage*: We can reliably detect resource records belonging to a wildcard.

- *Disadvantage*: We still cannot reliably detect the exceptions to
  the wildcard. We do not have the problem anymore, of detecting
  wildcard records that do not exist. But we still have the problem
  that *we cannot reliably detect the exceptions to the wildcards*.
  Again, if a wildcard resource record exists in combination with a
  load balancer, we get multiple different IPs for different queries.
  We cannot detect if these different responses are due to different
  DNS resource records or due to the load balancer returning different
  IPs for the same wildcard resource record.
  From the other perspective, if we know that we have a DNS wildcard
  record, then the web server at that IP may still serve different
  content depending on the virtual host that is requested. So in this
  scenario we also cannot reliably detect the exceptional domains.
