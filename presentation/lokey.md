---
marp: true
theme: lokey
paginate: true
size: 16:9
footer: 'Lokey'
---

<!-- _class: lead title -->
<!-- _paginate: false -->
<!-- _footer: '' -->

![Lokey](assets/logo_trans.png)

# Lokey

## Entropy for the stack you already run

Hardware randomness, fed into firmware, the kernel, virtio, and containers.

<!--
Open on the audience's stack. Lokey is software that feeds entropy into consumers they already have. The hardware under that software can be swapped. About twenty minutes, one claim at a time.
-->

---

# Cryptography spends entropy

- A cipher, a hash, or a signature preserves the unpredictability it is given.
- The search is the set of keys, nonces, salts, and seeds that might have been used.
- An algorithmic generator becomes easier to attack as quantum computing advances. A physical source is not a seed that can be stolen.

<!--
A hash, a key-derivation function, and a signature are deterministic. The width of the digest is not the size of the search. The attacker walks the inputs.
Quantum stays this one clause, as the site states it: algorithmic randomness gets easier to exploit, and physics-based entropy is not a stolen seed. Do not open a quantum section.
-->

---

# A small input set is a brute-force set

- Structured or guessable content shrinks that set.
- Short length bounds it, even when every bit looks noisy.
- A biased nonce can leak a private key from a sound signature scheme.
- A short one-time password, and a key minted at first boot, are the same thin set.

<!--
Two ways the candidate set gets small, taught as one idea: content and length.
The sharp case is a biased or reused nonce. ECDSA can be a correct algorithm and still expose the private key when the nonce was predictable.
Hold this. The Linux boot slide and the banking one-time password are the same failure, later, in ordinary clothes.
-->

---

# Randomness cannot be observed

- A finite sample can always be the output of a deterministic program.
- There is no procedure that finds the shortest such program.
- A cryptographic generator is built to be indistinguishable from an ideal source.
- Inspection of the output cannot certify the source.

<!--
Kolmogorov complexity is uncomputable, which is the formal name for the second line. You cannot prove a finite string random by reading it.
A CSPRNG is defined to pass as ideal randomness. If the construction works, no suite you run can tell the generator from a physical source.
This is why "the output looks random" does not repair a weak input. The next slide says what a test is still allowed to conclude.
-->

---

# A test can only reject

- Stripes, bias, repetition, and correlation mean the sample does not behave as randomness should.
- A clean result means we did not catch a deviation.
- Dieharder, a flat histogram, and a bitmap of static are rejection tests.
- The published plots later in the talk are held to this standard.

<!--
What a test can do is falsify. Absence of a finding is "we did not catch it."
Say this in those words again when the bitmap appears, so validation is the same claim as this slide.
-->

---

# These chips cannot be shown to be truly random

Entropy hardware in general-purpose machines is dominated by designs from the United States.

- Intel and AMD, on the CPU.
- NVIDIA, on the accelerator.
- The noise source sits in silicon too complex to inspect from outside.
- A generator that behaves well passes the same tests either way.

<!--
Do not claim a known backdoor. The claim is that these parts cannot be shown to be truly random.
The process question is blocked by the complexity of the chip. The output question is blocked by the previous two slides: a well-behaved generator passes Dieharder whether or not the analog source is honest.
Intel and AMD supply the CPU generators. NVIDIA supplies the accelerator. That is the market the audience already runs.
-->

---

# Linux accumulates entropy over time

- The kernel credits entropy across the life of the machine.
- At startup the pool is in a low-entropy state.
- Host keys, disk-encryption keys, and first service credentials are drawn then.
- A freshly cloned guest starts cold again, even when the image is old.

<!--
The pool fills over uptime. The dangerous window is before that pool has accumulated entropy: first boot, and every new guest kernel.
This is the brute-force point from the opening, timed to provisioning. Keys born in that window come from a thinner set.
Modern getrandom waits for initialization. The line to defend is the low-entropy state at startup, and clones that repeat it. Do not say that every later cryptographic call on a long-running Linux host is predictable.
-->

---

# Each layer inherits the one below

<ol class="ladder">
  <li><span>Firmware</span> Secure boot, device identity, and disk unlock. Before any OS.</li>
  <li><span>Hardware</span> The CPU and GPU generators, or a discrete element beside them.</li>
  <li><span>Kernel</span> The pool fills over time. It is thin at boot.</li>
  <li><span>Virtual kernel</span> virtio-rng delivers whatever the host injects.</li>
  <li><span>Containers</span> Pods share that kernel. A library spends what the kernel had.</li>
</ol>

<!--
Read top to bottom. A guest does not invent entropy. virtio-rng is the pipe. A container has no private pool, and a cloud-native pod is that container scheduled at scale.
A starved boot pool or an unauditable CPU source becomes the virtio feed and every pod's keys.
Integration has to happen on this ladder. An API that only applications call leaves firmware, the guest kernel, and unmodified pods untouched.
The sector slides each name one of these rungs in the corner.
-->

---

# Lokey feeds the consumers you already run

- Firmware reads a discrete part.
- The kernel pool is fed, and virtio-rng carries that feed into the guest.
- Containers inherit the host kernel, so the pod needs no code change.
- An API and a CLI remain for callers that want an explicit stream.

<!--
This is the product point, and it only lands because the ladder is already on screen.
Lokey's durable artifact is the software stack. Workflows keep the consumers they have.
Fortuna and the health checks belong to the path of a bit, two slides on. They harden raw noise. They are not a claim that randomness was observed.
-->

---

# The source under that stack can be swapped

- Now: the Microchip ATECC608B. Closed source, certified under NIST SP 800-90.
- It sits beside the CPU, and it is enough to prove the software integrates.
- Next: an open-source discrete circuit, itself certified, so the closed part can be retired.
- That circuit is the open hardware device on the site, aimed under €50.

<!--
NIST program name, confirmed from public records. Do not invent a number, and do not lead with one.
If asked: the ECC608 physical noise source is on NIST's SP 800-90B entropy validation for the ATECC608B, certificate E46. The ATECC608B counter DRBG is on the CAVP list, certificate C1777. Microchip's own documentation says the generator is designed to meet SP 800-90A, B, and C.
The €50 figure is the site's phase-1 aim for an open-source hardware device and ecosystem. It is a destination, not a second roadmap, and it is not a price we claim to ship today.
The closed part is a stand-in. The next slide says why that is compatible with the chip critique.
-->

---

# A bounded part can be removed

- The ATECC608B is a small certified module beside the CPU.
- The CPU and GPU generators are part of the machine.
- Behavioral tests still only falsify, for either source.
- The open circuit is how that source becomes something we can survey.

<!--
Say this so the closed chip is not heard as a retreat. The earlier critique was complexity, and a source that cannot be swapped out of the machine. The ATECC608B is bounded, certified, and removable. It exists to prove the software. The open discrete circuit is how the source later becomes surveyable.
Tests still only reject. Certification of the stand-in is not an observation of randomness.
-->

---

# Raw noise is hardened, then handed to a rung

- The ATECC608B amplifies thermal noise, outside the host CPU.
- Open-source health checks watch the stream for bias and faults.
- Fortuna, from Bruce Schneier and Niels Ferguson, mixes the raw bits.
- The result leaves through firmware, the kernel, virtio, or the API.

<!--
Four beats from the site: physical capture, open supervision, Fortuna, then delivery.
Fortuna is conditioning. It hardens the stream against a partial hardware failure. It does not certify that we observed randomness.
The source sits outside the host CPU, so a compromised OS or hypervisor does not determine the raw noise. That is the manifesto point.
If asked about the NIST write-up: certificate E46 describes the ECC608 source as a physical ring-oscillator noise source. The site describes that same noise as thermal. Same part, circuit name and physical mechanism.
-->

---

# The same ladder, in your room or in the EU

- On-device or air-gapped, the operator's systems hold the entropy.
- Hosted in the EU, the pipeline stays under GDPR.
- As the site states it, that path sits outside the Patriot Act and the CLOUD Act.
- Both modes feed firmware, kernels, guests, and pods.

<!--
Two delivery modes. Keep them distinct. Local and air-gapped is the manifesto and the FAQ: entropy need not leave the operator. The hosted API is the hero and the EU section.
Both feed the same ladder. Do not describe them as one promise.
-->

---

# We did not catch a deviation

<div class="plots">

<figure>
<img src="assets/plots/TRNG_bitmap_analysis_20251030_181202.png" alt="Bitmap of Lokey entropy bytes, shown as static">
<figcaption>Each pixel is one byte. Structure would show. This looks like static.</figcaption>
</figure>

<figure>
<img src="assets/plots/TRNG_frequency_distribution_20251030_181200.png" alt="Histogram of Lokey byte values from 0 to 255">
<figcaption>Byte values 0–255. A spike or a gap would be bias.</figcaption>
</figure>

</div>

<p class="stat">45 of 46 Dieharder tests. A healthy generator is expected to fail about 1–5%. Mean intensity about 127.5, standard deviation about 73.9. No cookies or trackers. The code is open.</p>

<!--
Read the title before the numbers. Nothing on this slide proves the source random. The pictures show no structure we could detect. That is the standard from the test slide.
45 of 46 is the published figure. The 1–5% caveat is the site's own: a single failure is expected for a healthy generator. Full logs are in the GitHub knowledge base.
Mean intensity about 127.5 and standard deviation about 73.9 are the site's figures for uniform byte coverage.
-->

---

# Four sectors, four rungs

- Government, at firmware and in the application.
- Banking and finance, at the HSM or KMS already in the room.
- Cloud, at the virtualized kernel and in cloud-native pods.
- AI and machine learning, in the application process.

<!--
These are the four cards on the site, tagged with the ladder. The facts on the next four slides stay inside those cards. The rung name in the corner is the only addition.
-->

---

<!-- _header: Firmware and application -->

# Government

<p class="k">Job</p>
<p>Identity, voting, notarization, and public lotteries.</p>
<p class="k">Failure</p>
<p>A signing key drawn from a thin boot pool is a small search with a public name.</p>
<p class="k">Lokey</p>
<p>Signature keypairs, certificate issuance, and draws that can be disclosed and challenged.</p>

<!--
Website facts only. Digital-signature keypairs and certificate issuance. Secure digital voting or lottery randomness that can be disclosed.
The failure line ties the boot-time pool to a public signing key. Integrity here means every key, token, or draw can be challenged.
-->

---

<!-- _header: Hardware appliance -->

# Banking and finance

<p class="k">Job</p>
<p>Transaction IDs, one-time passwords, onboarding, and device provisioning.</p>
<p class="k">Failure</p>
<p>A short password is a small search, whatever hash wraps it.</p>
<p class="k">Lokey</p>
<p>Verifiable entropy into the HSM or KMS seed pools you already run, and tamper-evident logs for auditors. Those boxes stay.</p>

<!--
The site is explicit: Lokey adds hardware entropy without ripping out existing HSMs. Say "those boxes stay" and mean it.
The short one-time password is the length side of the opening argument.
The second website bullet is tamper-evident randomness logs for audits and regulators.
-->

---

<!-- _header: virtio and containers -->

# Cloud

<p class="k">Job</p>
<p>Isolate tenants, encrypt workloads, and issue credentials that cannot be replayed.</p>
<p class="k">Failure</p>
<p>The guest receives the host's pool. Every pod on that kernel inherits it.</p>
<p class="k">Lokey</p>
<p>A per-tenant feed through virtio into VMs, containers, and serverless workloads, and into the secrets service.</p>

<!--
This is the inheritance slide made into the product. Website facts: per-tenant entropy for VM, container, and serverless; secrets management seeded by auditable randomness; API credentials that cannot be replayed.
virtio-rng is how the guest kernel is fed. Pods do not have a pool of their own.
-->

---

<!-- _header: Application -->

# AI and machine learning

<p class="k">Job</p>
<p>Weight initialization, shuffling, differential privacy, Monte Carlo sampling, and reinforcement learning.</p>
<p class="k">Failure</p>
<p>A guessable seed makes privacy noise and sampling predictable.</p>
<p class="k">Lokey</p>
<p>Hardware entropy where guessability is the risk. A recorded seed where the run must be replayed.</p>

<!--
The site says both "reproducible research" and "truly random seeds." Keep them apart. Record a seed when a run must be replayed. Read hardware entropy where guessability is the risk: privacy noise, unpredictable sampling.
A recorded seed is a deliberate, logged input. It is not a claim that the sample was observed to be random. Say that if someone asks. It is an editorial clarification of two lines that sit next to each other on the site.
-->

---

<!-- _class: lead -->
<!-- _paginate: false -->
<!-- _footer: 'Phase 2 and 3: early adopters, then geographic scale. Not this talk.' -->

# Integrate now. Survey the source next.

The software stack is what you integrate now, on the NIST SP 800-90 part.

The open certified discrete circuit is what we develop next.

Early adopters on the site: government, banking, and cloud.

[Knowledge base](https://github.com/LokeyTRaaS/Knowledgebase) · pieter@lokey.cloud

<!--
Close on the swap. What is integratable now is the software on the certified part. What is developed next is the open discrete circuit.
Early adopters named on the site are government, banking, and cloud. Phase 2 and 3 stay in the footer: developer portal, geographic sources, commercial support. Do not tour them.
The knowledge base holds the Dieharder logs. pieter@lokey.cloud is the contact on the site.
Existing workflows keep their consumers. The source under them becomes something that can be surveyed.
-->
