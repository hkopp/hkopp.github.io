---
layout: post
category : Writeup
tags : [writeup, hacking, ctf, crypto]
---
{% include math %}

## Intro
On 2020-04-17 I participated as part of the USS in [PlaidCTF](https://plaidctf.com).
As usual thanks to the
organizers and sponsors for putting together such nice challenges.

As usual in CTFs there were a bunch of challenges and if you solved
one correctly, a special flag in form of a binary string appears from
somewhere. This flag can be submitted in the web-interface and your
team gets points.

The USS team made the 134th place among the 541 participants, even
though we solved only one challenge.

This post contains my writeup of the challenge xorsa. If you want to
play around with the challenge, you can find it
[here]({{ site.baseurl }}/assets/2021-04-18-Writeup-of-Xorsa-from-PlaidCTF-2021/xorsa.tgz).
If you want to read other writeups, go
[here](https:////ctftime.org/event/1199/tasks/).

Note: This post also appeared on the [USS
site](https://uss.informatik.uni-ulm.de/2021/04/Writeup-of-Xorsa-from-PlaidCTF-2021)

## XORSA

The challenge consisted of three files:

- public.pem: An RSA public key in PEM file format
- xorsa.sage: A file with sage code.
- flag.enc: From the sage file and the file name it is suggested, that
  this is the encrypted flag.

Below is the content of the sage file.

{% highlight python %}
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP
from secret import p,q

x = 16158503035655503426113161923582139215996816729841729510388257123879913978158886398099119284865182008994209960822918533986492024494600106348146394391522057566608094710459034761239411826561975763233251722937911293380163746384471886598967490683174505277425790076708816190844068727460135370229854070720638780344789626637927699732624476246512446229279134683464388038627051524453190148083707025054101132463059634405171130015990728153311556498299145863647112326468089494225289395728401221863674961839497514512905495012562702779156196970731085339939466059770413224786385677222902726546438487688076765303358036256878804074494

assert p^^q == x

n = p*q
e = 65537
d = inverse_mod(e, (p-1)*(q-1))

n = int(n)
e = int(e)
d = int(d)
p = int(p)
q = int(q)

flag = open("flag.txt","rb").read().strip()
key = RSA.construct((n,e,d,p,q))
cipher = PKCS1_OAEP.new(key)
ciphertext = cipher.encrypt(flag)
open("flag.enc","wb").write(ciphertext)
open("private.pem","wb").write(key.exportKey())
open("public.pem","wb").write(key.publickey().exportKey())
{% endhighlight %}

This is very similar to the usual setup for RSA.
We have unknown prime numbers p, q. There is a modulus n=p*q.
We have the public part of the key e = 65537 and the secret part d
such that e*d = 1 mod (p-1)(q-1).
If we could factorize n and get to know p and q, we could compute the
secret d.
OAEP is used for padding, which is also fairly standard.

The suspicious part that screams "look at me" is that p^^q == x and x
is known. After reading previous discussions of my teammate I learned
that ^^ is sage syntax for the xor operation.

So maybe it is possible to factor n, as we know x.
I simply googled this and found clever people who already solved this
[here](https://math.stackexchange.com/questions/2087588/integer-factorization-with-additional-knowledge-of-p-oplus-q/2087589).
The idea is that we first look at the i least significant bits (i.e.,
mod 2**i) start with i=1 and factorize there.
This gives us the lowest bits of each, p and q. 
Then we increase i. As we know the xor,
we only have two more possibilities for the next bit of p to test.
We repeat this, until i covers teh whole number.
In the linked stackexchange discussion, there is [a link to a github
repo](https://github.com/sliedes/xor_factor), where someone has
already implemented this.

The next steps are thus as follows:
1. Extract n from the RSA public key.
2. Factorize n using the knowledge of x and the [github code](https://github.com/sliedes/xor_factor).
3. Decrypt the encrypted flag.

### Extracting n from the RSA public key

If you know the commands, this is fairly straightforward.
Running the following code shows n.

{% highlight python %}
from Crypto.PublicKey import RSA # pip install pycrypto
f = open("public.pem", "r")
key = RSA.importKey(f.read())
print(key.n) #displays n
{% endhighlight %}

### Factorizing n
Take the code from [here](https://github.com/sliedes/xor_factor) and
invoke it like this: `./xorfactor n x`, where you have to substitute n
and x with the correct values.

### Decrypting the flag
Now that we have all the components together, all that remains is
creating the private key and decrypting the flag.
For this, I was able to reuse some of the initial challenge code.
Note the assertion that `p*q == n`. Stuff like this is very important
for debugging when it would not have worked.

{% highlight python %}
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

p = 20420969679471891108678348706656014746583934953911779875638425062354608191587183527950669164730700934272964182495787953304429468689762991322834551415645126708376516740680832417842286228420321593951669261051536747972457884863137310884282408490497575698325423547287860485177580030001077220138986358776296171919687266364733033819652040824038462023264875486252347426522916763724808898841410387685709431644531618456599577633972666332007415430841589961044976519245509149515296930564608453957399860365835338361556249254149485870260959744131461233248546913266875137205814224895267413530734240509464368626036706116974009128413
q = 28054539427494619618149689905596076429856984445645433669516156290418225421410517787779668517055290133852649936139827864804362688012118333884178242727752944519074048590360750318612378539438159662786353326373062884835264801371859099448845473624420663390433385862519105545532536453262415924316279385374822224406717473538131244397856571015984808737311810992683556365367226442459037956423012864505787939824387975762988816118609146663560957688289879555734075738210834196560874913737409142987175846607589921563622580505680695004314201238943582440280860553616004146782317383144868597295249895821620037478662736479377036405283
n = 572900899020416333871774257897548190887875148332377649724629936942125065954403900214957099848244171042974830846321891115307194396859686148130195132775076147750816194194669485148310306866581747110109691391312495531105927432103095764487512855459663872226168973095665437985144973023035595602765438245069834002841752496072121527638243974956820747758308700622999731497265472265365706764203471894277850225555548962683741076063801485180774626107811063013748877832840560423294386298604594493033408101188433615350326658252746536427284017654908082042006370318751764459634138015874523739235598764251156235537699918418385721276093786930034069514119606912034739140729411915149355415250809598641184778583967357160241141516039721817350339432544637530638228659667851395488664406233933526970325653567096968977127473742267040558971420711170242569029577549402889853450812286141680939789725346428838857690827772743624748319239951407607711869461490939565401926953995816761514931996533209601647238748877564442428220404985707296616973140792150179655494712897659250302854615541193197046051998966799951879589911393813337960444303479065771250762320802542931155305758806716461516379431800232991995334538124086322560806306237096858377000246068189144665458605879
x = 16158503035655503426113161923582139215996816729841729510388257123879913978158886398099119284865182008994209960822918533986492024494600106348146394391522057566608094710459034761239411826561975763233251722937911293380163746384471886598967490683174505277425790076708816190844068727460135370229854070720638780344789626637927699732624476246512446229279134683464388038627051524453190148083707025054101132463059634405171130015990728153311556498299145863647112326468089494225289395728401221863674961839497514512905495012562702779156196970731085339939466059770413224786385677222902726546438487688076765303358036256878804074494
e = 65537
d = inverse_mod(e, (p-1)*(q-1))

assert p*q == n
assert p^^q == x

n = int(n)
e = int(e)
d = int(d)
p = int(p)
q = int(q)

ciphertext = open("flag.enc", "rb").read().strip()
key = RSA.construct((n,e,d,p,q))
cipher = PKCS1_OAEP.new(key)
flag = cipher.decrypt(ciphertext)
print(flag)
{% endhighlight %}


Running it yields the correct flag.

{% highlight python %}
hjgk@ophit:~/pctf/xorsa$ sage sol.sage
b'PCTF{who_needs_xor_when_you_can_add_and_subtract}'
{% endhighlight %}
