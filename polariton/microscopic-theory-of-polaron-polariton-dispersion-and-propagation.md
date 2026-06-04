pubs.acs.org/NanoLett 

Letter 

# Microscopic Theory of Polaron-Polariton Dispersion and Propagation

Logan Blackham, Arshath Manjalingal, Saeed Rahmanian Koshkaki, and Arkajit Mandal* 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/8488ce6322bd8ed8ffd2cf28233baae53d32567d4721acbe184d647a6b5640bf.jpg)


Cite This: Nano Lett. 2025, 25, 15874−15882 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/9cff9cd9aeae2873777dc10a10afe67a437f47fd0d5999e40bb79798aed54bd4.jpg)


Read Online 

ACCESS 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/0698686fe66ac9a006d7c66b258a7132e6e1bf251615b7b467f642a7b8ed87f9.jpg)


Metrics & More 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/3c78f876b828c447ab6fcaafcb9f2a4e41f25c0253e1202375f06b032b6d708e.jpg)


Article Recommendations 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/f3a7bc24d8e543527d5d81007b6896b52aae1794928c06e5fe2b21f2de49694f.jpg)


Supporting Information 

ABSTRACT: We develop an analytical, microscopic theory to describe polaron-polariton dispersion, formed by hybridizing excitons, photons, and phonons, as well as their coherent dynamics inside optical cavities. Starting from a microscopic light-matter Hamiltonian, we derive a simple analytical model by employing a nonperturbative treatment of the phonon and photon couplings to excitons. Within our theoretical framework, phonons are treated as classical fields, which are then quantized via the Floquet formalism. We show that, to a good approximation, the entire polaronpolariton system can be described with a band picture despite the phonons breaking translational symmetry. Our theory also sheds 

light on the long-lived coherent ballistic motion of exciton-polaritons with high excitonic character that propagate with group velocities lower than expected from pure exciton-polariton bands, offering a microscopic explanation for these puzzling experimental observations. 

KEYWORDS: Exciton-Polaritons, Exciton-Polariton transport, Exciton-Polariton dispersion, Light-Matter Interactions, Cavity Quantum Electrodynamics 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/55a15ef3a45248b7fab5c3e8eccc66cebc9fae32ed670b8b5e227cc8ba3d5d21.jpg)


![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/8f32b36cda2ed3207d1515ad50d8c5ea4e9b42e19bd54aa22c6087d17c548efe.jpg)


Coupling quantized electromagnetic radiation to excitons forms exciton-polaritons (EPs), a hybrid photon-matter quasi-particle, that demonstrates a wide range of exotic phenomena,8 16 including enhanced transport surpassing the ordinary phenomenon, namely cavity-enhanced exciton transport, demonstrates the unique nature of exciton-polaritons, redefining the traditional paradigms of energy transport with possible applications in quantum information science and chemical reactivity.5,7,23 

A superposition of neighboring exciton states in reciprocal space leads to coherent ballistic propagation with a group velocity equal to the slope of the band structure in the absence of dissipation.24 Phonons, which are intrinsic to materials, break the translational symmetry of an excitonic system, leading to phonon-induced decoherence and incoherent diffusive motion.25−28 At the same time, it is expected that the coherent ballistic motion of exciton-polaritons will exhibit group velocities matching the exciton-polariton dispersion for times less than the decoherence lifetime.7,29,30 Interestingly, recent experiments18,19,31,32 indicate that exciton-polaritons with significantly high excitonic character (up to ∼50% excitonic) show long-lived coherent ballistic motion (up to hundreds of femtoseconds)17−19,33 with group velocities lower than the slopes of the exciton-polariton band structure extracted from the linear spectra.5,18,19,32 Despite many recent insightful theoretical works on exciton-polariton dynamics,18,34−40 a full microscopic understanding of this extraordinary phenomenon has remained elusive. This includes a recent study37 that computed group-velocity renormalization within a perturbative framework; however, it does not reproduce the correct polaritonic dispersion nor establish its connection to the experimentally observed group velocities and does not provide an explanation to the long-lived coherence of polaritons. 

Here, we introduce a new theoretical framework to understand the complex polariton dispersion formed by hybridizing excitons, photons, and phonons, as well as investigating their coherent dynamics inside optical cavities. Given the intractable nature of the full quantum mechanical problem, we introduce a convenient picture where excitonpolaritons are embedded in a classical phonon field. We quantize this phonon field using the Floquet formalism to derive an analytical model exhibiting translational symmetry to a good approximation (near k → 0 where the experiments also operate18,19,41), allowing for coherent motion. This analytical model produces an extremely accurate description of excitonpolariton dispersion when compared to the angle-resolved 

Received: August 12, 2025 

Revised: October 13, 2025 

Accepted: October 15, 2025 

Published: October 20, 2025 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/45dfdf9085dc6f0daf4fe992dbfd2d382416db1ef91c733f811bd1324a8001ea.jpg)


polariton spectra obtained using a mixed quantum-classical approach.18,34−37 Using our model, we show that the presence of phonons introduces vibronic structure in the excitonpolariton dispersion, which we refer to as the polaron-polariton dispersion. We show that this vibronic structure, which is beyond the scope of a perturbative treatment of phonon interactions implemented in recent work,37 is responsible for a renormalization of the group velocity and despite a strong interaction with phonons, an effective band structure model can be extracted. Our theory not only serves as a convenient analytical model to understand polariton spectra but also provides new insights into the interplay between phonons and exciton-polaritons. 

We consider a generalized multimode Holstein-Tavis-Cummings Hamiltonian,7,8,34,42 which describes an excitonpolariton system beyond the long-wavelength approximation, interacting with phonons and is written as 

$$
\begin{array}{l} \hat {H} _ {\mathrm{LM}} = \sum_ {n} \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n} \varepsilon_ {0} + \sum_ {n, k} \frac {\Omega_ {k}}{\sqrt {N}} [ \hat {a} _ {k} ^ {\dagger} \hat {X} _ {n} e ^ {- i k \cdot r _ {n}} + \hat {a} _ {k} \hat {X} _ {n} ^ {\dagger} e ^ {i k \cdot r _ {n}} ] + \\ \sum_ {k} \hat {a} _ {k} ^ {\dagger} \hat {a} _ {k} \omega_ {c} (k) + \tau \sum_ {n} (\hat {X} _ {n} ^ {\dagger} \hat {X} _ {n + 1} + \hat {X} _ {n + 1} ^ {\dagger} \hat {X} _ {n}) + \\ \sum_ {n, j} \gamma_ {j} \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n} \hat {R} _ {n, j} + \sum_ {n, j} \frac {\hat {P} _ {n , j} ^ {2}}{2} + \frac {1}{2} \omega_ {j} ^ {2} \hat {R} _ {n, j} ^ {2} \\ = \hat {H} _ {\mathrm{EP}} + \sum_ {n, j} \gamma_ {j} \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n} \hat {R} _ {n, j} + \sum_ {n, j} \frac {\hat {P} _ {n , j} ^ {2}}{2} + \frac {1}{2} \omega_ {j} ^ {2} \hat {R} _ {n, j} ^ {2} \tag {1} \\ \end{array}
$$

Here $\hat { X } _ { n } ^ { \dag } \left( \hat { a } _ { k } ^ { \dag } \right)$ creates an excitation (photon) at site n (mode $k ) , R _ { n , j } \ : ( P _ { n , j } )$ is the position (momentum) operator for the jth phonon mode at the nth site, and $\hat { H } _ { \mathrm { E P } }$ in the last line is the pure exciton-photon Hamiltonian. Here $\varepsilon _ { 0 }$ is the on-site energy with each site located at $r _ { n } = a \cdot n$ with $r _ { n + N } = r _ { n }$ where N is the total number of sites, a is the lattice constant, and τ is the hopping parameter. Further, $\{ \omega _ { j } \}$ and $\{ \gamma _ { j } \}$ are the phonon frequencies and its coupling to a excitonic site which are sampled from a spectral density $\begin{array} { r } { J ( \omega ) = \sum _ { j } \frac { \gamma _ { j } ^ { 2 } } { \omega _ { j } } \delta ( \omega - \omega _ { j } ) } \end{array}$ . 

Finally, $\omega _ { c } ( k )$ and $\Omega _ { k } = \Omega \sqrt { \omega _ { c } ( 0 ) / \omega _ { c } ( k ) }$ are the photon frequency and exciton-photon couplings, respectively. Additionally, we restrict our system to the single excited subspace, and do not consider nonlinear interactions or many-body effects.43 F urther details are provided in the Supporting Information (SI). Notably, the phonon degrees of freedom break the translational symmetry of the exciton-polariton system. Nevertheless, we demonstrate that a quasi-band structure framework can be employed, effectively capturing the complex ballistic transport of exciton-polaritons. 

Direct (analytical or numerical) quantum mechanical treatment of this light-matter Hamiltonian is a formidable task given that polaritonic dispersion can only be obtained when using $N \sim \mathrm { 1 0 ^ { 5 } }$ sites for experimentally relevant values of the lattice constant a (chosen here to be 1.2 nm). To solve this intractable problem, we employ a mixed-quantum-classical approach, namely the mean-field Ehrenfest (MFE) method,7,44,45 that is known to accurately reproduce quantum vibronic structure in optical spectra in a single-site exciton− phonon model,46,47 despite the classical treatment of phonons. Note that the primary drawback of the MFE approach is its 

which leads to inaccurate longtime population dynamics, even though it yields accurate correlation functions at short times.46,49,50 In the present work, we compute only those correlation functions that require short-time propagation and focus exclusively on dynamics up to roughly 200 fs. In the $\mathrm { S I } ,$ we compare numerically exact results for few-site models (up to seven sites) with the MFE approach, demonstrating that this simple mixed quantum− classical method produces results of reasonable accuracy, in agreement with previous studie s.46,49−51 In spite of the quasiclassical treatment of the phonons, MFE can capture the quantum vibronic progression which becomes exact for low frequencies or at high temperatures.52 

These calculations reveal that while the relative peak positions of the photonic spectral function are reproduced by the MFE with reasonable accuracy, the corresponding peak heights (intensities) can deviate, albeit without any qualitative discrepancies. Within this approach, the phonon modes are treated quasi-classically, i.e. $\{ \hat { R } _ { n , j } , \hat { P } _ { n , j } \}  \{ R _ { n , j } , P _ { n , j } \}$ , while the photonic and excitonic parts are propagated quantum mechanically using the polaritonic Hamiltonian $\begin{array} { r } { \hat { H } _ { \mathrm { p l } } ( \mathbf { R } ) = \hat { H } _ { \mathrm { L M } } - \frac { 1 } { 2 } \underset { n , j } { \sum } \big ( \mathbf { \bar { \cal P } } _ { n , j } ^ { 2 } - \omega _ { j } ^ { \bar { 2 } } R _ { n , j } ^ { 2 } \big ) } \end{array}$ . The equations of mo-

tion in the MFE approach (in atomic units) are written as 

$$
i | \dot {\Psi} (t) \rangle = \hat {H} _ {\mathrm{pl}} (\mathbf {R}) | \Psi (t) \rangle \tag {2}
$$

$$
\ddot {R} _ {n, j} (t) = \dot {P} _ {n, j} (t) = - \left\langle \Psi (t) \left| \frac {\hat {H} _ {\mathrm{LM}} (\mathbf {R})}{R _ {n , j}} \right| \Psi (t) \right\rangle \tag {3}
$$

The initial nuclei positions and momentums $\{ R _ { n , j } ( 0 ) _ { \lambda }$ , $P _ { n , j } ( 0 ) \}$ are sampled from a Wigner distribution (see details in the SI), and an expectation value of an operator $\hat { A }$ is computed as $\langle \hat { A } \rangle \approx \langle \langle \Psi ( t ) | \hat { A } | \Psi ( t ) \rangle \rangle _ { \mathrm { M F E } } ,$ , where $\langle \cdots \rangle _ { \mathrm { M F E } }$ indicates averaging over realizations of initial nuclear coordinates $\{ R _ { n , j } ( 0 ) , P _ { n , j } ( 0 ) \}$ }. 

The angle-resolved photonic spectral function $I ( \omega , \ k )$ is obtained by computing 

$$
\begin{array}{l} I (\omega , k) = \operatorname{Re} \left[ \lim _ {\mathcal {T} \rightarrow \infty} \int_ {0} ^ {\mathcal {T}} d t e ^ {i \omega t} \langle \langle 1 _ {k} | \Psi (t) \rangle \rangle_ {\mathrm{MFE}} \cdot \right. \\ \left. \cos (\pi t / 2 \mathcal {T}) \right] \\ \end{array}
$$

where $| \Psi ( 0 ) \rangle = \hat { a } _ { k } ^ { \dagger } | \overline { { 0 } } \rangle = | 1 _ { k } \rangle$ , with |0 as the vacuum state. Note that we have included the term cos $\left( \pi t / 2 \mathcal { T } \right)$ to suppress spurious Gibbs oscillations. Our numerical results, presented in Figure 1, illustrates the emergence of complex vibronic structure in the momentum-resolved polaritonic spectra in the presence of phonon modes. As can be seen in these figures, despite the absence of a strict translational symmetry, the angle-resolved spectra suggests the existence of a quasi-band of polaron-polaritons. Such vibronic structure in exciton-polariton derive the analytical forms of these quasi-bands with details provided in the Supporting Information. 

To obtain an analytical expression for these polaronpolariton (quasi) bands, we first make the classical path approximation $, ^ { 2 7 , 5 6 , 5 7 }$ such that $\ddot { R } _ { n , j } ( t ) \approx - \omega _ { j } ^ { 2 } R _ { n , j } ( t )$ with 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/75d1cf6d50099ccd27c744de12e80730b8e5ee2c20566958a29fc2f94ce01d79.jpg)



Figure 1. (a) Schematic illustration model of exciton-polariton transport inside an optical cavity. (b)−(e) Polaron-polariton dispersion obtained with MFE (simulation) compared to our analytical theory (at room temperature $T = 3 0 0 \mathrm { ~ K ~ } )$ with different phonon couplings: (b) $\gamma _ { 0 } = 0 , \mathrm { ~ ( c ) ~ } \gamma _ { 0 } = \overline { { { \gamma _ { 0 } } } } / 2 , \mathrm { ~ ( d ) ~ } \gamma _ { 0 } = \overline { { { \gamma _ { 0 } } } } ,$ , and (e) $\gamma _ { 0 } = 3 \overline { { \gamma _ { 0 } } } / 2$ , where $\overline { { \gamma _ { 0 } } } = 5 . 8 5 \times 1 0 ^ { - 4 } \mathrm { ~ a . u . }$ . Further, we use $\Omega = 2 4 0 0$ $\mathrm { c m } ^ { - 1 } , N = 4 0 0 0 1 , \tau = 0 , \omega _ { c } ( 0 ) = 2 . 5 8 \mathrm { e V } ,$ and $\varepsilon _ { 0 } = 3 . 2 \ \mathrm { e V } .$


$$
R _ {n, j} (t) \approx R _ {n, j} (0) \cos \omega_ {j} t + \frac {1}{\omega_ {j}} P _ {n, j} (0) \sin \omega_ {j} t \tag {4}
$$

With this analytical expression of $R _ { n , i } ( t ) .$ , the dynamics of the exciton-polariton wave function |Ψ⟩ can be thought to be evolving under the time-periodic Hamiltonian $\hat { H } _ { \mathrm { p l } } ( t )$ expressed as 

$$
\hat {H} _ {\mathrm{pl}} (t) = \hat {H} _ {\mathrm{EP}} + \sum_ {j} \left(\hat {P} _ {j} e ^ {\mathrm{i} \omega_ {j} t} + \hat {P} _ {j} ^ {\dagger} e ^ {- \mathrm{i} \omega_ {j} t}\right) \tag {5}
$$

where $\hat { P } _ { j } = \sum _ { n } \gamma _ { j } \hat { X } _ { n } ^ { \dag } \hat { X } _ { n } Z _ { n , j }$ describes the interaction to a classical n phonon field with $\begin{array} { r } { Z _ { n , j } = \frac { R _ { n , j } ( 0 ) } { 2 } + \frac { P _ { n , j } ( 0 ) } { 2 i \omega _ { i } } } \end{array}$ i 2 j . Note that the classical path approximation remains valid for the exciton-polariton dynamics confined to the single excited subspace (relevant to the present study) and that it can break down at high excitations where many-body effect can also persist.43 

Notice the similarity between $\hat { H } _ { \mathrm { p l } } ( t )$ and the typical lasermatter Hamiltonian, with phonon degrees of freedom (or molecular vibrations) in our system playing the same role as a laser field. We adapt the Floquet formalism58−63 and rewrite $\hat { H } _ { \mathrm { p l } } ( t )$ in an extended space (so-called Sambe space) as a timeindependent Hamiltonian $\hat { H } _ { \mathrm { F } }$ such that 

$$
\hat {H} _ {\mathrm{pl}} (t) \rightarrow \hat {H} _ {\mathrm{F}} = \lim _ {M \to \infty} \sum_ {\alpha , \beta} | \beta \rangle \langle \beta | \hat {\mathcal {H}} _ {\mathrm{F}} | \alpha \rangle \langle \alpha |
$$

$$
\text { with } \{| \alpha \rangle , | \beta \rangle \} \in \left\{\hat {X} _ {n} ^ {\dagger} \prod_ {j} \frac {(\widehat {B} _ {j} ^ {\dagger}) ^ {M + m _ {j}}}{\sqrt {(M + m _ {j}) !}} | \overline {{0}} \rangle , \hat {a} _ {k} ^ {\dagger} \right.
$$

$$
\left. \prod_ {j} \frac {\left(\widehat {B} _ {j} ^ {\dagger}\right) ^ {M + m _ {j}}}{\sqrt {(M + m _ {j}) !}} | \overline {{{0}}} \rangle \right\} \tag {6}
$$

Here, $\hat { \mathcal { H } } _ { \mathrm { F } }$ denotes the quantized Floquet Hamiltonian expressed in the extended basis {|α⟩, |β⟩}, which includes photonic or excitonic states carrying m excitations in the collective phonon f ield (in the limit $M ~ \to ~ \infty )$ . We have introduced the bosonic operator $\hat { B } _ { j } ^ { \dagger }$ to create an excitation in this collective phonon field of frequency $\omega _ { j } .$ Note that the typical multimode Floquet formalism becomes intractable for noncommensurate frequencies, since it formally requires an infinite basis in the extended Hilbert space. However, owing to the structure of light−matter coupling used here, we find that only a finite and computationally tractactble number of Floquet states is needed to achieve convergence. 

The time-independent Hamiltonian in the Sambe-space, $\hat { \mathcal { H } } _ { \mathrm { F } }$ is expressed as 

$$
\begin{array}{l} \hat {\mathcal {H}} _ {\mathrm{F}} = \sum_ {n, j} \left(\varepsilon_ {0} + \frac {\gamma_ {j}}{\sqrt {M}} (Z _ {n, j} \hat {B} _ {j} + Z _ {n, j} ^ {*} \hat {B} _ {j} ^ {\dagger})\right) \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n} \\ + \tau \sum_ {n} (\hat {X} _ {n} ^ {\dagger} \hat {X} _ {n + 1} + \hat {X} _ {n + 1} ^ {\dagger} \hat {X} _ {n}) + \sum_ {j} \omega_ {j} (\hat {B} _ {j} ^ {\dagger} \hat {B} _ {j} - M) \\ + \sum_ {k} \hat {a} _ {k} ^ {\dagger} \hat {a} _ {k} \omega_ {c} (k) + \sum_ {n, k} \frac {\Omega_ {k}}{\sqrt {N}} \left(\hat {a} _ {k} ^ {\dagger} \hat {X} _ {n} e ^ {- i k \cdot r _ {n}} + \hat {a} _ {k} \hat {X} _ {n} ^ {\dagger} e ^ {i k \cdot r _ {n}}\right) \tag {7} \\ \end{array}
$$

Next, we perform a polaron transformation on $\hat { \mathcal { H } } _ { \mathrm { F } }$ using the operator $\hat { U } _ { D }$ defined as 

$$
\hat {U} _ {D} = \prod_ {n, j} \exp \left[ \left(Z _ {n, j} ^ {*} \hat {B} _ {j} ^ {\dagger} - Z _ {n, j} \hat {B} _ {j}\right) \frac {\gamma_ {j} \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n}}{\omega_ {j} \sqrt {M}} \right] \tag {8}
$$

to obtain $\hat { \mathcal { H } } _ { \mathrm { F } } ^ { ' } = \hat { U } _ { D } ^ { \dagger } \hat { \mathcal { H } } _ { \mathrm { F } } \hat { U } _ { D }$ that is explicitly written as 

$$
\begin{array}{l} \hat {\mathcal {H}} _ {\mathrm{F}} ^ {\prime} = \sum_ {n} \varepsilon_ {0} \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n} + \sum_ {j} (\hat {B} _ {j} ^ {\dagger} \hat {B} _ {j} - M) \omega_ {j} + \sum_ {k} \hat {a} _ {k} ^ {\dagger} \hat {a} _ {k} \omega_ {c} (k) \\ + \tau \sum_ {n} \left(\hat {X} _ {n} ^ {\dagger} \hat {X} _ {n + 1} \prod_ {j} \exp \left[ \gamma_ {j} \frac {\Delta Z _ {n , j} \hat {B} _ {j} - \Delta Z _ {n , j} ^ {*} \hat {B} _ {j} ^ {\dagger}}{\omega_ {j} \sqrt {M}} \right] \right. \\ \left. + h. c.\right) + \sum_ {n, k} \frac {\Omega_ {k}}{\sqrt {N}} \left(\hat {a} _ {k} ^ {\dagger} \hat {X} _ {n} \right. \\ \prod_ {j} \exp \left[ \frac {\gamma_ {j} (Z _ {n , j} \hat {B} _ {j} - Z _ {n , j} ^ {*} \hat {B} _ {j} ^ {\dagger})}{\omega_ {j} \sqrt {M}} - i k \cdot r _ {n} \right] + h. c. \tag {9} \\ \end{array}
$$

Here $\Delta Z _ { n , j } ~ = ~ Z _ { n + 1 , j } ~ - ~ Z _ { n , j } .$ Below, we make further simplifications to obtain a convenient expression to obtain the polaron polariton quasi-bands, with details provided in the 

SI. First, we restrict the extended subspace to include only $m _ { j } =$ 0 when considering photonic excitations such that 

$$
\{| \alpha \rangle , | \beta \rangle \} \in \left\{\hat {X} _ {n} ^ {\dagger} \prod_ {j} \frac {(\widehat {B} _ {j} ^ {\dagger}) ^ {M + m _ {j}}}{\sqrt {(M + m _ {j}) !}} | \overline {{0}} \rangle , \hat {a} _ {k} ^ {\dagger} \prod_ {j} \frac {(\widehat {B} _ {j} ^ {\dagger}) ^ {M}}{\sqrt {M !}} | \overline {{0}} \rangle \right\}
$$

Second, we introduce the following ef fective reciprocal (phonon-dressed) exciton operators 

$$
\hat {Y} _ {k, \vec {\mathbf {m}}} ^ {\dagger} = \lim _ {M \rightarrow \infty} \sum_ {n} e ^ {- i k \cdot r _ {n}} \hat {X} _ {n} ^ {\dagger} \prod_ {j} \frac {Q _ {0 m _ {j}} ^ {[ j ]} (Z _ {n , j})}{\sqrt {\mathcal {S} _ {m _ {j}} ^ {[ j ]} (M + m _ {j}) !}} (\hat {B} _ {j} ^ {\dagger}) ^ {M + m _ {j}} \tag {10}
$$

where $\vec { \bf m } = ( m _ { 0 } , m _ { 1 } , . . . ) , S _ { m _ { j } } ^ { [ j ] } = \sum _ { n } \left| { \cal Q } _ { 0 m _ { j } } ^ { [ j ] } ( Z _ { n , j } ) \vert ^ { 2 } \right.$ is a normalization factor and $Q _ { 0 m _ { i } } ^ { [ j ] } ( Z _ { n , j } )$ is the overlap between infinitely excited displaced Fock states of the jth phonon mode written as 

$$
Q _ {0 m _ {j}} ^ {[ j ]} (\tilde {Z}) = \lim _ {M \rightarrow \infty} \left\langle \right. M \left. \right| \exp \left[ - \frac {\gamma_ {j}}{\omega_ {j} \sqrt {M}} \left(\tilde {Z} \widehat {B _ {j}} - \tilde {Z} ^ {*} \hat {B _ {j}} ^ {\dagger}\right)\right] | M + m _ {j} \rangle \tag {11}
$$

where $\tilde { Z }$ is a complex number. In the SI, we show that $\langle \overline { { { 0 } } } | \hat { Y } _ { k , \vec { \mathbf { m } } ^ { ' } } \hat { Y } _ { k , \vec { \mathbf { m } } } ^ { \dagger } | \overline { { { 0 } } } \rangle \approx \delta _ { \vec { \mathbf { m } } , \vec { \mathbf { m } } ^ { ' } } \left( \mathrm { f o r } \ k \to 0 \right)$ , using which we write 

$$
\begin{array}{l} \hat {H} _ {\mathrm{F}} ^ {\prime} \approx \sum_ {k} [ \hat {a} _ {k} ^ {\dagger} \hat {a} _ {k} \omega_ {c} (k) + \sum_ {\vec {\mathbf {m}}, j} (\overline {{\varepsilon}} + m _ {j} \omega_ {j}) \hat {Y} _ {k, \vec {\mathbf {m}}} ^ {\dagger} \hat {Y} _ {k, \vec {\mathbf {m}}} \\ + \sum_ {\vec {\mathbf {m}}} \prod_ {j} \sqrt {\frac {\mathcal {S} _ {m _ {j}} ^ {[ j ]}}{N}} \Omega_ {k} (\hat {Y} _ {k, \vec {\mathbf {m}}} ^ {\dagger} \hat {a} _ {k} + \hat {a} _ {k} ^ {\dagger} \hat {Y} _ {k, \vec {\mathbf {m}}}) ] \\ = \sum_ {k} \hat {\mathcal {H}} _ {k} \tag {12} \\ \end{array}
$$

w h e r e 

$$
\overline {{\varepsilon}} = \varepsilon_ {0} + 2 \tau \cdot \xi_ {0}
$$

w i t h 

$$
\xi_ {0} = \sum_ {n} \prod_ {j} Q _ {0 0} ^ {[ j ]} (\Delta Z _ {n, j}) \times Q _ {0 m _ {j}} ^ {[ j ]} (Z _ {n, j}) \times Q _ {0 m _ {j}} ^ {[ j ]} (Z _ {n + 1, j}) / \mathcal {S} _ {m _ {j}} ^ {[ j ]} \mathrm{de-}
$$

scribing a phonon induced suppression of the hopping term $\tau .$ Here $\hat { H } _ { \mathrm { F } } ^ { ' }$ is block diagonal in $k ,$ thus allowing us to extract the phonon-modified exciton-polariton (or equivalently polaron-polariton) dispersion. In the case of a single phonon-mode per site with $\begin{array} { r } { J ( \omega ) = \frac { \gamma _ { 0 } ^ { 2 } } { \omega _ { 0 } } \delta ( \omega - \omega _ { 0 } ) } \end{array}$ , polaron-polariton (quasi) bands are obtained by diagonalizing a simplified $\hat { \mathcal { H } } _ { k }$ matrix written as 

$$
\hat {\mathcal {H}} _ {k} =
$$

$$
\left[ \begin{array}{c c c c c c} \ddots & \vdots & \vdots & \vdots & & \vdots \\ ... & \overline {{\varepsilon}} - \omega_ {0} & 0 & 0 & ... & \sqrt {\frac {S _ {- 1} ^ {[ 0 ]}}{N}} \Omega_ {k} \\ ... & 0 & \overline {{\varepsilon}} & 0 & ... & \sqrt {\frac {S _ {0} ^ {[ 0 ]}}{N}} \Omega_ {k} \\ ... & 0 & 0 & \overline {{\varepsilon}} + \omega_ {0} & ... & \sqrt {\frac {S _ {1} ^ {[ 0 ]}}{N}} \Omega_ {k} \\ & \vdots & \vdots & \vdots & \ddots & \vdots \\ ... & \sqrt {\frac {S _ {- 1} ^ {[ 0 ]}}{N}} \Omega_ {k} & \sqrt {\frac {S _ {0} ^ {[ 0 ]}}{N}} \Omega_ {k} & \sqrt {\frac {S _ {1} ^ {[ 0 ]}}{N}} \Omega_ {k} & ... & \omega_ {c} (k) \end{array} \right] \tag {13}
$$

Overall, we find that phonon interactions modify the exciton polariton bands in specifically two ways. 

First, it introduces vibronic states, which are effectively captured via the collective phonon field excitations via $\hat { B } _ { j } ^ { \dagger }$ within our mixed quantum-classical framework, coupling to the photonic bands forming a Rabi-Splitting of $\Omega _ { k } = \prod _ { j } \sqrt { S _ { m _ { j } } ^ { [ j ] } / N }$ . This can be calculated by integrating the squared overlap $\Pi { [ Q _ { m 0 } ^ { [ j ] } ( Z _ { n , j } ) ] } ^ { 2 }$ over the Wigner distribution of $\{ R _ { n , j } ( 0 )$ , j $P _ { n , j } ( 0 ) \}$ such that 

$$
\begin{array}{l} \mathcal {S} _ {m _ {j}} ^ {[ j ]} = \lim _ {M \rightarrow \infty} 2 \tanh \left(\frac {\beta \omega_ {j}}{2}\right) \int_ {- \infty} ^ {\infty} d z \frac {M !}{(M + m _ {j}) !} \left(\frac {\gamma_ {j} ^ {2} z ^ {2}}{\omega_ {j} ^ {2} M}\right) ^ {m _ {j}} \\ \times \exp \left[ - \left(4 \omega_ {j} \tanh \left(\frac {\beta \omega_ {j}}{2}\right) + \frac {z _ {j} ^ {2} \gamma_ {j} ^ {4}}{4 \omega_ {j} ^ {4} M ^ {2}}\right) z ^ {2} \right] \\ \left[ L _ {M} ^ {(m _ {j})} \left(\frac {\gamma_ {j} ^ {2} z ^ {2}}{\omega_ {j} ^ {2} M}\right) \right] ^ {2} \tag {14} \\ \end{array}
$$

Here, $\beta = ( k _ { \mathrm { B } } T ) ^ { - 1 }$ with $k _ { \mathrm { B } }$ the Boltzmann constant and $T =$ 300 K is the temperature. Further, $L _ { M } ^ { ( m _ { j } ) }$ is the associated Laguerre polynomial. Note that the model presented in eq 12- 14, is structurally different from previously proposed models for polaritonic spectra64−66 that incorporated the vibronic progression in the polariton dispersion in an ad-hoc manner. 

Second, the phonons renormalize the hopping term $\tau _ { r }$ shifting up the excitonic energy near $k  0$ as expected. In the following, we will focus on the vibronic structure and its implication for the polariton dispersion and set $\tau = 0$ (relevant for molecular exciton-polaritons) and consider a single mode per site with J( ) = $\begin{array} { r } { J ( \omega ) = \frac { \gamma _ { 0 } ^ { 2 } } { \omega _ { 0 } } \delta ( \omega - \omega _ { 0 } ) } \end{array}$ which is adequate for describing various organic molecules that have a rigid structure (such as polycyclic aromatic hydrocarbons). In the ${ \mathrm { S I } } ,$ we present results when $\tau \neq 0$ relevant for exciton-polaritons in extended materials. In the SI, we also present results when considering multiple phonon modes per site, described with the spectral density $\begin{array} { r } { J ( \omega ) = \sum _ { j } \frac { \gamma _ { j } ^ { 2 } } { \omega _ { j } } \delta ( \omega - \omega _ { j } ) } \end{array}$ , and find similar polaron-polariton dynamics (such as group velocity renormalization) and spectra with the same level of accuracy of our analytical approach (as in the main-text). This illustrates the applicability of our approach in a wider range of model systems. 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-06-04/63732783-3672-401d-9274-c7e4a58f39ca/b24b15c19cd137f67aca365388a0dfe266a143e0f477530b62ca48fa083d4d77.jpg)



Figure 2. (a)-(b) Phonon modified exciton polariton group velocities obtained using direct mixed quantum classical simulations (filled circles) compared to our analytical theory (solid lines) at a phonon frequency (a) $\omega _ { 0 } = 1 4 4 0 \mathrm { { c m } ^ { - 1 } }$ and (b) $\omega _ { 0 } = 3 6 0 ~ \mathrm { c m ^ { - 1 } }$ with various phonon couplings. In panel (a) we consider $\gamma _ { 0 } = 0$ (black), $\gamma _ { 0 } = \overline { { \gamma } } _ { 0 } / 2$ (red), $\gamma _ { 0 } = \overline { { \gamma } } _ { 0 }$ (green), and $\gamma _ { 0 } = 3 \overline { { \gamma _ { 0 } } } / 2$ (blue) with $\overline { { \gamma _ { 0 } } } = 5 . 8 5 \times { 1 0 } ^ { - 4 }$ a.u. In panel (b) we consider $\gamma _ { 0 } = 0 \ \mathrm { ( b l a c k ) } , \gamma _ { 0 } = \overline { { \gamma } } _ { 0 } / 2 \ \mathrm { ( r e d ) } , \gamma _ { 0 } = \overline { { \gamma } } _ { 0 } \ \mathrm { ( g r e e n ) }$ and $\gamma _ { 0 } = 3 \overline { { \gamma _ { 0 } } } / 2$ (blue) with $\overline { { \gamma } } _ { 0 } = 1 . 4 6 \times 1 0 ^ { - 4 } \mathrm { a . u . } \mathrm { ( c ) - ( d ) }$ Time-dependent polariton density in the (c) absence and (d) presence of phonon couplings. (e) Polaron-polariton dispersion computed using MFE and our analytical approach (blue solid lines) for the same parameters as in the blue curve in (b). Further, we use $\Omega = \overline { { { 2 4 0 0 ~ \mathrm { c m } ^ { - 1 } } , \tau } } = 0 , \omega _ { c } ( \bar { 0 ) } = 2 . 5 8 ~ \mathrm { e V } ,$ and $\varepsilon _ { 0 } = 3 . 2 \ \mathrm { e V }$ and $N = 4 0 0 0 1$ in panel (a) and N = 30001 for (b).


Figure 1 presents the angle-resolved polariton spectra comparing with polariton (quasi) bands obtained using our analytical model presented in eq 13. Figure 1a schematically illustrates an excitonic material placed inside a Fabry-Perot́ cavity exhibiting ballistic transport of polaritons. The angleresolved polariton spectra in the absence of phonons, presented in Figure 1b reduce to the two-band coupled oscillator model used widely.3,7,8 Our theoretical model (solid blue lines) reduces to this two-band model when setting $\gamma _ { 0 } = 0$ (no phonon interactions), thus exactly reproducing the polaritonic spectra in Figure 1b. 

Figure 1c-e presents the polariton spectra obtained from our mixed-quantum classical simulation at various phonon couplings $\gamma _ { 0 } ,$ comparing them to the predictions of our analytical model. The vibronic structure shown in Figure 1c-e has been reported in multiple experimental works16,41,53−55 with our analytical model offering a microscopic understanding of the polariton spectra. Our nonperturbative treatment of phonon couplings is essential for capturing these vibronic features. We emphasize that, when considering multiple phonon modes per site, we find that the vibronic structure diminishes (see Figures S7 and S8), as is the case for molecular spectra. Thus, whether or not these vibronic structures will show up in the polariton spectra depends entirely on the spectral density of the system under consideration. Our theory operates conveniently in all of such circumstances. 

At relatively small phonon coupling $\gamma _ { 0 } = \overline { { \gamma } } _ { 0 } / 2$ (we set $\overline { { \gamma _ { 0 } } } = 5 . 8 5 \times 1 0 ^ { - 4 } \mathrm { ~ a . u . } )$ , we observe a clear phonon-induced splitting of the lower polariton band around 3 eV. Note that with increasing phonon coupling, the exciton on-site energy minima along the phonon displacement coordinate is shifted down by the reorganization energy $\begin{array} { r } { \lambda = \frac { 1 } { 2 } \frac { \gamma _ { 0 } ^ { 2 } } { \omega _ { 0 } ^ { 2 } } , } \end{array}$ As a result, increasing $\gamma _ { 0 }$ also leads to the formation of phonon-induced splitting at progressively lower energy. At the same time, increasing $\gamma _ { 0 }$ also introduces more splitting. This is because the increase in the displacement of the phonon field leads to more sizable overlap between the photonic states and excitonic states with m phonon field excitation or de-excitation in the Floquet picture employed here. 

With the success in obtaining the phonon-modified polariton bands (which we refer to as polaron polariton bands) using our analytical model, in Figure 2 we use it to obtain the group velocities and provide insights into the coherent propagation of polaron-polaritons. In the absence of phonons, a coherent superposition of neighboring wave vectors 

in the momentum space, e.g. $\left| \Psi \right. = \operatorname* { l i m } _ { F \delta k \to 0 } \frac { 1 } { \sqrt { F } } \sum _ { n = 1 } ^ { F } \left| k + n \delta k \right.$ F in an extended system, leads to coherent ballistic propagation with a group velocity equal to the slope of the band structure $d E / d k ,$ known as the group velocity. On the other hand, in the presence of phonons, phonon-induced decoherence leads to incoherent diffusive motion. Therefore, it is expected that exciton-polaritons, depending on the extent of their material character, will lead to short-time (for times less than the decoherence lifetime) coherent ballistic motion with group velocities matching the exciton-polariton dispersion. Experimental results, however, indicate that exciton-polaritons with significantly high excitonic character (up to ∼ 50% excitonic) show long-lived coherent ballistic motion (up to hundreds of femtoseconds) with group velocities lower than the slopes of the exciton-polariton band structure. Our theory provides a plausible explanation for this 2-fold mystery and provides new microscopic insights into this extraordinary phenomenon. 

Figure 2a-b presents the polariton group velocity obtained from our analytical model (solid lines), comparing it to the group velocities obtained by performing direct mixed quantum-classical simulations (filled circles) at two different phonon frequencies and various phonon couplings. Figure 2c-d presents time-dependent polaritonic density 

$$
\rho_ {n} (t) = \langle \langle \Psi (t) | \hat {X} _ {n} ^ {\dagger} \hat {X} _ {n} + \hat {a} _ {n} ^ {\dagger} \hat {a} _ {n} | \Psi (t) \rangle \rangle_ {\mathrm{MFE}}
$$

in the presence of (d) and absence of (c) phonon couplings. We have prepared the initial exciton-polariton wave function as a linear combination of polariton states within an energy window ΔE centered at an excitation energy $E _ { 0 } ,$ such that $\begin{array} { r } { | \Psi ( 0 ) \rangle = \sum c _ { j } | E _ { j } \rangle } \end{array}$ with $E _ { 0 } - \Delta E / 2 < E _ { i } < E _ { 0 } + \mathrm { ~ \bar { \Delta } E / 2 ~ }$ and $| E _ { j } \rangle$ as the eigenstates of $\hat { H } _ { \mathrm { E P } } .$ . In both cases, we observe a ballistic propagation suggested by the linear expansion of the wavefront in time, with (d) propagating relatively slowly compared to (c). We extract the group velocities from these wavefronts, which are presented in Figure 2a-b (filled circles) and are compared to the predictions of our analytical model. 

At higher phonon frequencies, the vibronic structure in the dispersion directly results in an oscillatory behavior in the group velocity with troughs separated by the phonon frequency $\omega _ { 0 } .$ . At lower phonon frequencies, such as in Figure 2b, the oscillatory structure is almost absent as the peaks in the analytical theory pack closer. Figure 2e presents the angleresolved spectra at $\omega _ { 0 } = 3 6 0 ~ \mathrm { c m ^ { - 1 } }$ where the vibronic peaks are no longer visible due to the finite line width of the optical spectra leading to a broadening at $k / a \approx 1 . 5 \times 1 0 ^ { - 3 }$ a.u. (see Figure S9). Therefore, even though the vibronic structure is not visible in polaritonic spectra, it results in a renormalization of the group velocity. This phenomenon has been observed experimentally,18,19 with our theory providing a clear theoretical explanation. 

In both scenarios, however, the observed group velocities are always lower, due to the formation of the polaron-polariton (quasi) bands that have flatter slopes, due to the contribution of the flat effective exciton bands $\hat { Y } _ { k , m } ,$ compared to the bare exciton-polariton dispersion. This renormalization of the exciton-polariton group velocity is induced by the presence of phonons in materials, and even at low phonon frequencies, where the vibronic structure in the angle-resolved spectra may be hidden due to various sources of dissipation (such as cavity loss), the quasi-bands lead to the renormalization of the group velocity. Overall, our theoretical model correctly captures the complex ballistic propagation of exciton-polaritons in the presence of phonon interactions and introduces a quasi-band picture that can be adopted to describe and understand the coherent propagation of polaron-polaritons. 

Importantly, our work also suggests a microscopic explanation for the relatively long-lived coherent propagation of exciton-polaritons with high exciton character5,18,19,32 at room temperature. We hypothesize that the origin of this extraordinary effect is the block diagonal nature of eq 10 where photon modes $\hat { a } _ { k } ^ { \dagger }$ couple to a particular set of ef fective reciprocal (phonon-dressed) excitons $\hat { Y } _ { k , m }$ with matching $k ,$ defined in eq 10. To clearly understand the ramifications of this, consider first a bare excitonic system coupled with phonons under laser driving (t) that target a subspace $\mathcal { K }$ in reciprocal space, which can be written as 

$$
\begin{array}{l} \hat {H} _ {\mathrm{X}} + \hat {H} _ {\mathrm{laser}} = \sum_ {k} \hat {X} _ {k} ^ {\dagger} \hat {X} _ {k} \epsilon_ {k} + \frac {\gamma_ {0}}{\sqrt {2 \omega_ {0}}} \sum_ {k, q} \hat {X} _ {k + q} ^ {\dagger} \hat {X} _ {k} (\hat {b} _ {q} + \hat {b} _ {- q} ^ {\dagger}) \\ + \sum_ {k} \hat {b} _ {k} ^ {\dagger} \hat {b} _ {k} \omega_ {0} + \mathcal {E} (t) \sum_ {k \in \mathcal {K}} \left(\hat {X} _ {k} ^ {\dagger} + \hat {X} _ {k}\right) \tag {15} \\ \end{array}
$$

where $\begin{array} { r } { \hat { b } _ { q } ^ { \dagger } = \frac { 1 } { \sqrt { N } } \sum _ { n } \hat { b } _ { n } ^ { \dagger } e ^ { i q r _ { n } } } \end{array}$ † R b b n( ) ,0n n = +† , and ϵk = ϵ0 + 2τ $\begin{array} { r } { \frac { ( \hat { b } _ { n } ^ { \dagger } + \hat { b } _ { n } ) } { \sqrt { 2 \omega _ { 0 } } } = \hat { R } _ { n , 0 } , } \end{array}$ $\epsilon _ { k } = \epsilon _ { 0 } + 2 \tau$ 

cos $( k \cdot a )$ for the choice of nearest neighbor interactions made here. Despite a laser exclusively targeting the subspace ${ \mathcal { K } } ,$ the population leaks out to the subspace $M = 1 - \mathcal { K }$ via the phonon-induced scattering term $\frac { \gamma _ { 0 } } { \sqrt { 2 \omega _ { 0 } } } \sum _ { k , q } \hat { X } _ { k + q } ^ { \dagger } \hat { X } _ { k } ( \hat { b } _ { q } + \hat { b } _ { - q } ^ { \dagger } )$ . In k q, contrast, inside an optical cavity, following our analytical model in eq 12, a driven light-matter hybrid system can be modeled $\mathsf { a s } ^ { 6 7 }$ 

$$
\begin{array}{l} \hat {H} _ {\mathrm{LM}} + \hat {H} _ {\mathrm{laser}} = \hat {H} _ {\mathrm{F}} ^ {\prime} + \mathcal {E} (t) \sum_ {k \in \mathcal {K}} (\hat {a} _ {k} ^ {\dagger} + \hat {a} _ {k}) \\ \approx \sum_ {k \in \mathcal {K}} \left[ \hat {\mathcal {H}} _ {k} + \mathcal {E} (t) \left(\hat {a} _ {k} ^ {\dagger} + \hat {a} _ {k}\right) \right] + \sum_ {k \in \mathcal {M}} \hat {\mathcal {H}} _ {k} \tag {16} \\ \end{array}
$$

such that the subspace and $\mathcal { K }$ now remain decoupled. Therefore, light-matter interaction also plays a crucial role in suppressing phonon-induced scattering in the reciprocal space (suppressing Fröhlich scattering of the energetically localized excitation), allowing for relatively long-lived ballistic motion in the time scale of hundreds of femtoseconds. That said, while a quantitative description of the decoherence lifetime associated with the ballistic-to-diffusive transition18,19,36,68,69 in excitonpolariton transport is beyond the scope of the present work, extending our framework to capture this behavior is part of our future goals. 

In summary, we developed a convenient theoretical framework to understand and predict the angle-resolved polariton spectra in the presence of phonon interactions. Starting from a microscopic Hamiltonian describing the interactions between phonons, excitons, and photons inside an optical cavity, we develop an analytical model that accurately predicts the complex angle-resolved polariton spectra and the group velocities of coherently propagating exciton-polaritons. We derive this analytical model by describing the phonons as time-periodic fields that are nonperturbatively interacting with exciton-polaritons and quantize them using the Floquet formalism that is typically used to describe laser-matter interactions. This work provides a new perspective into the observed renormalization of polaritonic group velocity and their long-lived coherent nature in recent experiments. Finally, we note that the accuracy of the present approach is limited, especially at lower temperatures or when nuclear quantum effect become dominant, due to the mixed-quantum classical treatment of phonons. Thus, an exact open quantum dynamical simulation of exciton−polaritons at room temperature would be highly desirable. 

## ASSOCIATED CONTENT



(20) Sanvitto, D.; Kéna-Cohen, S. The road towards polaritonic devices. Nature materials 2016, 15, 1061−1073. 





(63) Oka, T.; Kitamura, S. Floquet engineering of quantum materials. Annual Review of Condensed Matter Physics 2019, 10, 387−408. 



## *sı Supporting Information



(21) Ferreira, B.; Rosati, R.; Malic, E. Microscopic modeling of exciton-polariton diffusion coefficients in atomically thin semiconductors. Physical Review Materials 2022, 6, 034008. 





(64) Spano, F. Optical microcavities enhance the exciton coherence length and eliminate vibronic coupling in J-aggregates. J. Chem. Phys. 2015, 142, 184707. 



The Supporting Information is available free of charge at https://pubs.acs.org/doi/10.1021/acs.nanolett.5c04134. 



(22) Dhamija, S.; Son, M. Mapping the dynamics of energy relaxation in exciton−polaritons using ultrafast two-dimensional electronic spectroscopy. Chemical Physics Reviews 2024, 5, 041309. 





(65) Mazza, L.; Kéna-Cohen, S.; Michetti, P.; La Rocca, G. C. Microscopic theory of polariton lasing via vibronically assisted scattering. Physical Review B Condensed Matter and Materials Physics 2013, 88, 075321. 



Details of the microscopic theory of polaron-polariton dispersion, initialization of the exciton-polariton wave function via Monte Carlo sampling, additional results for comparing to exact diagonalization and quantum perturbation theory, absorption spectra of multiphonon systems, anharmonic and temperature considerations, and finite cavity lifetime, and parameters used for simulations (PDF) 



(23) Head-Marsden, K.; Flick, J.; Ciccarino, C. J.; Narang, P. Quantum information and algorithms for correlated quantum matter. Chem. Rev. 2021, 121, 3061−3120. 





(66) Fontanesi, L.; Mazza, L.; La Rocca, G. C. Organic-based microcavities with vibronic progressions: Linear spectroscopy. Physical Review B Condensed Matter and Materials Physics 2009, 80, 235313. 



## AUTHOR INFORMATION



(24) Reineker, P. Exciton dynamics in molecular crystals and aggregates; Springer: 1982; Vol. 94. 





(67) Herrera, F.; Spano, F. C. Absorption and photoluminescence in organic cavity QED. Phys. Rev. A 2017, 95, 053867. 



## Corresponding Author



(25) Troisi, A.; Orlandi, G. Charge-Transport Regime of Crystalline Organic Semiconductors: Diffusion Limited by Thermal Off-Diagonal Electronic Disorder. Physical review letters 2006, 96, 086601. 





(68) Pandya, R.; Chen, R. Y.; Gu, Q.; Sung, J.; Schnedermann, C.; Ojambati, O. S.; Chikkaraddy, R.; Gorman, J.; Jacucci, G.; Onelli, O. D.; et al. Microcavity-like exciton-polaritons can be the primary photoexcitation in bare organic semiconductors. Nat. Commun. 2021, 12, 6519. 



Arkajit Mandal − Department of Chemistry, Texas A&M University, College Station, Texas 77843, United States; orcid.org/0000-0001-9088-2980; Email: mandal@ tamu.edu 



(26) Fetherolf, J. H.; Golez,̌ D.; Berkelbach, T. C. A unification of the Holstein polaron and dynamic disorder pictures of charge transport in organic crystals. Physical Review X 2020, 10, 021062. 





(69) Sokolovskii, I.; Luo, Y.; Groenhof, G. Disentangling enhanced diffusion and ballistic motion of excitons coupled to Bloch surface waves with molecular dynamics simulations. J. Phys. Chem. Lett. 2025, 16, 6719−6727. 



## Authors



(27) Wang, L.; Beljonne, D.; Chen, L.; Shi, Q. Mixed quantumclassical simulations of charge transport in organic materials: Numerical benchmark of the Su-Schrieffer-Heeger model. J. Chem. Phys. 2011, 134, 244116. 





Logan Blackham − Department of Chemistry, Texas A&M University, College Station, Texas 77843, United States 





(28) Yarkony, D. R.; Silbey, R. Variational approach to exciton transport in molecular crystals. J. Chem. Phys. 1977, 67, 5818−5827. 





Arshath Manjalingal − Department of Chemistry, Texas A&M University, College Station, Texas 77843, United States; orcid.org/0009-0008-3261-9699 





(29) Balasubrahmaniyam, M.; Genet, C.; Schwartz, T. Coupling and decoupling of polaritonic states in multimode cavities. Phys. Rev. B 2021, 103, L241407. 





Saeed Rahmanian Koshkaki − Department of Chemistry, Texas A&M University, College Station, Texas 77843, United States; orcid.org/0000-0002-5557-4509 





(30) Krupp, N.; Groenhof, G.; Vendrell, O. Quantum dynamics simulation of exciton-polariton transport. Nat. Commun. 2025, 16, 5431. 





Complete contact information is available at: https://pubs.acs.org/10.1021/acs.nanolett.5c04134 





(31) Berghuis, A. M.; Tichauer, R. H.; de Jong, L. M. A.; Sokolovskii, I.; Bai, P.; Ramezani, M.; Murai, S.; Groenhof, G.; Gómez Rivas, J. Controlling Exciton Propagation in Organic Crystals through Strong Coupling to Plasmonic Nanoparticle Arrays. ACS Photonics 2022, 9, 2263−2272. 



## Author Contributions



(32) Pandya, R.; Ashoka, A.; Geirgiou, K.; Sung, J.; Jayaprakash, R.; Renken, S.; Gai, L.; Shen, Z.; Rao, A.; Musser, A. J. Tuning the Coherent Propagation of Organic Exciton-Polaritons through Dark State Delocalization. Advances Science 2022, 9, 2105569. 



L.B. and A. Manjalingal contributed equally. 



(33) Liu, B.; Menon, V. M.; Sfeir, M. Y. The Role of Long-Lived Excitons in the Dynamics of Strongly Coupled Molecular Polaritons. ACS Photonics 2020, 7, 2292−2301. 



## Notes



(34) Sokolovskii, I.; Tichauer, R. H.; Morozov, D.; Feist, J.; Groenhof, G. Multi-scale molecular dynamics simulations of enhanced energy transfer in organic molecules under strong coupling. Nat. Commun. 2023, 14, 6613. 



The authors declare no competing financial interest. 



(35) Tichauer, R. H.; Sokolovskii, I.; Groenhof, G. Tuning the Coherent Propagation of Organic Exciton-Polaritons through the Cavity Q-factor. Advanced Science 2023, 10, 2302650. 



## ACKNOWLEDGMENTS



(36) Chng, B. X. K.; Mondal, M. E.; Ying, W.; Huo, P. Quantum Dynamics Simulations of Exciton Polariton Transport. Nano Lett. 2025, 25, 1617−1622. 



This work was supported by the Texas A&M startup funds. This work used TAMU FASTER at the Texas A&M University through allocation PHY230021 from the Advanced Cyberinfrastructure Coordination Ecosystem: Services & Support (ACCESS) program, which is supported by National Science Foundation grants #2138259, #2138286, #2138307, #2137603, and #2138296. A. Mandal appreciates inspiring discussions with Micheal Taylor, Daniel Tabor, Milan Delor, Pengfei Huo, David R. Reichman, Wenxiang Ying, Dipti Jasrasaria and Haimi Nguyen. The authors appreciate discussions with Pritha Ghosh and Sachith Wickramasinghe. 



(37) Ying, W.; Chng, B. X.; Delor, M.; Huo, P. Microscopic theory of polariton group velocity renormalization. Nat. Commun. 2025, 16, 6950. 





(38) Tutunnikov, I.; Qutubuddin, M.; Sadeghpour, H. R.; Cao, J. Characterization of polariton dynamics in a multimode cavity: Noiseenhanced ballistic expansion. arXiv preprint October 2024. arXiv:2410.11051 [physics.optics]. https://arxiv.org/abs/2410.11051. 



## REFERENCES



(39) Aroeira, G. J.; Kairys, K. T.; Ribeiro, R. F. Coherent transient exciton transport in disordered polaritonic wires. Nanophotonics 2024, 13, 2553−2564. 





(40) Engelhardt, G.; Cao, J. Polariton Localization and Dispersion Properties of Disordered Quantum Emitters in Multimode Microcavities. Phys. Rev. Lett. 2023, 130, 213602. 





(1) Li, T. E.; Cui, B.; Subotnik, J. E.; Nitzan, A. Molecular polaritonics: Chemical dynamics under strong light−matter coupling. Annu. Rev. Phys. Chem. 2022, 73, 43−71. 





(41) Hong, Y.; Xu, D.; Delor, M. Exciton delocalization suppresses polariton scattering. Chem. 2025, 102759. 





(2) Snoke, D.; Littlewood, P. Polariton condensates. Phys. Today 2010, 63, 42−47. 





(3) Deng, H.; Haug, H.; Yamamoto, Y. Exciton-polariton Bose− Einstein condensation. Rev. Mod. Phys. 2010, 82, 1489−1537. 





(42) Mandal, A.; Xu, D.; Mahajan, A.; Lee, J.; Delor, M.; Reichman, D. R. Microscopic Theory of Multimode Polariton Dispersion in Multilayered Materials. Nano Lett. 2023, 23, 4082−4089. 





(4) Lidzey, D. G.; Bradley, D. D. C.; Virgili, T.; Armitage, A.; Skolnick, M. S.; Walker, S. Room Temperature Polariton Emission from Strongly Coupled Organic Semiconductor Microcavities. Phys. Rev. Lett. 1999, 82, 3316−3319. 





(43) Ghosh, P.; Manjalingal, A.; Wickramasinghe, S.; Koshkaki, S. R.; Mandal, A. Mean-field mixed quantum-classical approach for many-body quantum dynamics of exciton polaritons. Phys. Rev. B 2025, 112, 104319. 





(5) Sandik, G.; Feist, J.; García-Vidal, F. J.; Schwartz, T. Cavityenhanced energy transport in molecular systems. Nat. Mater. 2025, 24, 344. 





(44) Hoffmann, N. M.; Lacombe, L.; Rubio, A.; Maitra, N. T. Effect of many modes on self-polarization and photochemical suppression in cavities. J. Chem. Phys. 2020, 153, 104103. 





(6) Kowalewski, M.; Bennett, K.; Mukamel, S. Non-adiabatic dynamics of molecules in optical cavities. J. Chem. Phys. 2016, 144, 054309. 





(45) Li, T. E.; Nitzan, A.; Sukharev, M.; Martinez, T.; Chen, H.-T.; Subotnik, J. E. Mixed quantum-classical electrodynamics: Understanding spontaneous decay and zero-point energy. Phys. Rev. A 2018, 97, 032105. 





(7) Mandal, A.; Taylor, M. A.; Weight, B. M.; Koessler, E. R.; Li, X.; Huo, P. Theoretical Advances in Polariton Chemistry and Molecular Cavity Quantum Electrodynamics. Chem. Rev. 2023, 123, 9786. 





(46) Egorov, S. A.; Rabani, E.; Berne, B. J. On the Adequacy of Mixed Quantum-Classical Dynamics in Condensed Phase Systems. J. Phys. Chem. B 1999, 103, 10978−10991. 





(8) Keeling, J.; Kéna-Cohen, S. Bose−Einstein Condensation of Exciton-Polaritons in Organic Microcavities. Annu. Rev. Phys. Chem. 2020, 71, 435−459. 





(47) Petit, A. S.; Subotnik, J. E. How to calculate linear absorption spectra with lifetime broadening using fewest switches surface hopping trajectories: A simple generalization of ground-state Kubo theory. J. Chem. Phys. 2014, 141, 014107. 





(9) Fan, Y.; Wan, Q.; Yao, Q.; Chen, X.; Guan, Y.; Alnatah, H.; Vaz, D.; Beaumariage, J.; Watanabe, K.; Taniguchi, T.; et al. others High efficiency of exciton-polariton lasing in a 2d multilayer structure. ACS photonics 2024, 11, 2722−2728. 





(48) Parandekar, P. V.; Tully, J. C. Detailed balance in Ehrenfest mixed quantum-classical dynamics. J. Chem. Theory Comput. 2006, 2, 229−235. 





(10) Frisk Kockum, A.; Miranowicz, A.; De Liberato, S.; Savasta, S.; Nori, F. Ultrastrong coupling between light and matter. Nature Reviews Physics 2019, 1, 19−40. 





(49) Van Der Vegte, C.; Dijkstra, A.; Knoester, J.; Jansen, T. Calculating two-dimensional spectra with the mixed quantum-classical ehrenfest method. J. Phys. Chem. A 2013, 117, 5970−5980. 





(11) Xu, D.; Peng, Z. H.; Trovatello, C.; Cheng, S.-W.; Xu, X.; Sternbach, A.; Basov, D. N.; Schuck, P. J.; Delor, M. Spatiotemporal imaging of nonlinear optics in van der Waals waveguides. Nat. Nanotechnol. 2025, 20, 374−380. 





(50) Karsten, S.; Ivanov, S. D.; Bokarev, S. I.; Kühn, O. Quasiclassical approaches to vibronic spectra revisited. J. Chem. Phys. 2018, 148, 102337. 





(12) Wurdack, M.; Yun, T.; Katzer, M.; Truscott, A.; Knorr, A.; Selig, M.; Ostrovskaya, E.; Estrecho, E. Negative-mass exciton polaritons induced by dissipative light-matter coupling in an atomically thin semiconductor. Nat. Commun. 2023, 14, 1026. 





(51) Nguyen, H.; Mandal, A.; Mahajan, A.; Reichman, D. R. Mixed quantum-classical methods for polaron spectral functions. J. Chem. Phys. 2025, 163, 114105. 





(13) Wurdack, M.; Estrecho, E.; Todd, S.; Yun, T.; Pieczarka, M.; Earl, S. K.; Davis, J. A.; Schneider, C.; Truscott, A.; Ostrovskaya, E. Motional narrowing, ballistic transport, and trapping of roomtemperature exciton polaritons in an atomically-thin semiconductor. Nat. Commun. 2021, 12, 5366. 





(52) Mukamel, S. On the semiclassical calculation of molecular absorption and fluorescence spectra. J. Chem. Phys. 1982, 77, 173− 181. 





(14) Suyabatmaz, E.; Ribeiro, R. F. Vibrational polariton transport in disordered media. J. Chem. Phys. 2023, 159, 034701. 





(53) Polak, D.; Jayaprakash, R.; Lyons, T. P.; Martínez-Martínez, L. Á .; Leventis, A.; Fallon, K. J.; Coulthard, H.; Bossanyi, D. G.; Georgiou, K.; Petty, A. J.; et al. Manipulating molecules with strong coupling: harvesting triplet excitons in organic exciton microcavities. Chemical science 2020, 11, 343−354. 





(15) Liu, X.; Galfsky, T.; Sun, Z.; Xia, F.; Lin, E.-c.; Lee, Y.-H.; Kéna-Cohen, S.; Menon, V. M. Strong light−matter coupling in twodimensional atomic crystals. Nat. Photonics 2015, 9, 30−34. 





(54) Li, D.; Shan, H.; Rupprecht, C.; Knopf, H.; Watanabe, K.; Taniguchi, T.; Qin, Y.; Tongay, S.; Nuß, M.; Schröder, S.; et al. Hybridized exciton-photon-phonon states in a transition metal dichalcogenide van der Waals heterostructure microcavity. Phys. Rev. Lett. 2022, 128, 087401. 





(16) Kéna-Cohen, S.; Forrest, S. Room-temperature polariton lasing in an organic single-crystal microcavity. Nat. Photonics 2010, 4, 371− 375. 





(55) Amano, M.; Otsuka, K.; Fujihara, T.; Kondo, H.; Bando, K. Slow-light dispersion of a cavity polariton in an organic crystal microcavity. Appl. Phys. Lett. 2022, 120, 133301. 





(17) Steger, M.; Liu, G.; Nelsen, B.; Gautham, C.; Snoke, D. W.; Balili, R.; Pfeiffer, L.; West, K. Long-range ballistic motion and coherent flow of long-lifetime polaritons. Phys. Rev. B 2013, 88, 235314. 





(56) Akimov, A. V.; Prezhdo, O. V. The PYXAID program for nonadiabatic molecular dynamics in condensed matter systems. J. Chem. Theory Comput. 2013, 9, 4959−4972. 





(18) Xu, D.; Mandal, A.; Baxter, J. M.; Cheng, S.-W.; Lee, I.; Su, H.; Liu, S.; Reichman, D. R.; Delor, M. Ultrafast imaging of polariton propagation and interactions. Nat. Commun. 2023, 14, 4082−4089. 





(57) Yamijala, S. S.; Huo, P. Direct Nonadiabatic Simulations of the Photoinduced Charge Transfer Dynamics. J. Phys. Chem. A 2021, 125, 628−635. 





(19) Balasubrahmaniyam, M.; Simkhovich, A.; Golombek, A.; Sandik, G.; Ankonina, G.; Schwartz, T. From enhanced diffusion to ultrafast ballistic motion of hybrid light−matter excitations. Nat. Mater. 2023, 22, 338−344. 





(58) Shirley, J. H. Solution of the Schrödinger equation with a Hamiltonian periodic in time. Phys. Rev. 1965, 138, B979. 





(59) Taylor, M.; Mandal, A.; Huo, P. Light-matter interaction hamiltonians in cavity quantum electrodynamics. Chemical Physics Reviews 2025, 6, 011305. 





(60) Bukov, M.; D'Alessio, L.; Polkovnikov, A. Universal high frequency behavior of periodically driven systems: from dynamical stabilization to Floquet engineering. Adv. Phys. 2015, 64, 139−226. 





(61) Ng, N.; Kolodrubetz, M. Many-Body Localization in the Presence of a Central Qudit. Phys. Rev. Lett. 2019, 122, 240402. 





(62) Koshkaki, S. R.; Kolodrubetz, M. H. Inverted many-body mobility edge in a central qudit problem. Phys. Rev. B 2022, 105, L060303. 

