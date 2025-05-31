There is this paper [Model Theory and Machine Learning](https://arxiv.org/abs/1801.06566) which made me do a complete double-take. Initially, when
I first saw this paper, I thought "There is no way these two fields are related". This sparked my curiosity and now, after weeks of
thinking about
it (I
chose this
paper as the
starting point for my thesis project) I realized that they are basically the same thing, connected at a very deep, foundational level. This kind
of insight is usually a rare occurrence for me, so I figured I should share it.

To make it accessible for both ML and logic people I will start with a quick primer for both sides. This blog post is supposed to be a light read 
so I consciously decided to stay light on the definitions and mathematical details.
&nbsp;

&nbsp;

**Computational Learning Theory (the ML side)**

Computational learning theory is a sub-branch of machine learning with actual math instead of vibes.
The core question is: given some probability distribution you can't see directly, how many samples do you need before you can make decent
predictions? And more importantly - are there things that are just fundamentally unlearnable no matter how much data you throw at them? If the
number of samples is finite then the probability distribution is learnable, otherwise it isn't.

The results are mostly of theoretical importance (in practice, you just run stochastic gradient descent until it works) but you can check
out [Conference on Learning Theory
(COLT)](https://learningtheory.org/colt2025/) if you want to follow the latest research in this area.

&nbsp;

**Stability theory (the logic side)**

When I first heard about model theory, I thought about differential equations and dynamical systems, but actually it is a branch of  
mathematical logic with an algebraic twist. Stability theory is a sub-branch of model theory started in the 60s, when Israeli mathematician Saharon
Shelah basically laid foundation for the entire discipline. His main insight was the "main gap" theorem - every mathematical theory is either:

&nbsp;- *stable:* The theory has "few" models in each uncountable cardinality - specifically, for any uncountable cardinal $$κ$$, the theory
has at most $$κ$$ models of cardinality $$κ$$ (up to isomorphism). This represents maximal control over the model-theoretic structure.

&nbsp;- *unstable:* The theory has "many" models - specifically, there exists some uncountable cardinal κ such that the theory has $$2^κ$$
distinct models of cardinality $$κ$$. This explosion in the number of models indicates a fundamental complexity that prevents the kind of
classification possible in the stable case.

The "main gap" refers to the fact that there's no middle ground: every complete theory in a countable language either has this controlled,
minimal growth
in the number of models (stable) or experiences this exponential explosion (unstable), so either you can tame the beast or it's chaos all the way
down.

Modern stability theory developed a whole taxonomy of tameness: ω-stable, superstable, stable theories, etc. It's like pokémon but
for mathematical structures.
You can check
out [forkinganddividing](https://www.forkinganddividing.com/) for the full bestiary.

&nbsp;

**The connection (this is where it gets interesting)**

Here's the thing that is so surprising: mathematically, both fields are asking the exact same question.

&nbsp;- Learning theory asks: "when can you learn a pattern from examples?"

&nbsp;- Stability theory asks: "when can you describe all models of a theory?"

In both cases you're trying to figure out when some mathematical object is "nice enough" to be completely
characterized vs when it's too chaotic to pin down. The sample complexity bounds in learning theory? That's a measure of how "stable" your
hypothesis class is. The classification of theories in stability theory? That's a measure how "learnable" mathematical
structures are.

&nbsp;

Both fields discovered that there's this fundamental dichotomy in mathematics between order and chaos, underpinned by
the [Sauer-Shelah lemma](https://en.wikipedia.org/wiki/Sauer%E2%80%93Shelah_lemma), but
they approached it from completely different angles. The ML people came from statistics and probability, the logic people came from set theory and
model theory. But still, they're studying the same underlying phenomenon: when does structure emerge from examples, and when does it all fall apart
into incomprehensible mess?

&nbsp;

**Circles: a concrete example**

*From the learning theory side:* There's some unknown target circle generating your data. You get iid samples $$(x_i, y_i)$$ where $$y_i = 1$$
if $$x_i$$ is inside the circle and $$0$$ otherwise. Your hypothesis class $$\mathcal{H}$$ consists of all possible circle interiors in $$\mathbb{R}
^2$$. Can you find $$h \in \mathcal{H}$$ that approximates the target circle well enough to classify new points reliably?

Answer is yes, since the [VC dimension](https://en.wikipedia.org/wiki/Vapnik%E2%80%93Chervonenkis_theory) of this concept class is exactly 3. VC dimension measures how many points you can "shatter"
—meaning you can label them arbitrarily using concepts from your class. For circles, you can shatter at most 3 points. Finding 4 points that you
can label in every possible way using circle interiors is impossible due to geometric constraints. This finite VC dimension guarantees that the 
class is PAC learnable -- with high probability, a hypothesis consistent with enough training examples
will generalize well to new data. 

What is important, that informally, the VC dimension measures the expressiveness of your concept class. It makes sense that if the concept class 
is infinitely expressive, then there is no algorithm to learn it from finite amount of data.

&nbsp;

*From the model theory side:*: Consider circles in the plane $$\mathbb{R}^2$$. Our target concept for learning is characterized by a single 
first-order formula in the theory of [real closed fields](https://en.wikipedia.org/wiki/Real_closed_field):

\\[ \varphi(x_1, x_2; y_1, y_2, y_3) = (x_1 - y_1)^2 + (x_2 - y_2)^2 < y_3 \\]

This formula naturally defines the interior of the circle, 
$$\{ (x_1,x_2)\in \mathbb{R}^2 : \varphi(x_1,x_2;y_1,y_2,y_3) \text{ is true} \}$$. If we take their union over all possible values of $$(y_1,y_2,y_3)
\in \mathbb{R}^3$$ we get what is otherwise known as a *uniformly definable family of sets* $$C_\varphi$$. Uniform means that the whole family of 
sets is definde by a single formula $$\varphi$$. Now, it is a well-known fact that the formula is [NIP](https://en.wikipedia.org/wiki/NIP_
(model_theory)), thus the family has a "tame" branching behavior, meaning you can't create unbounded branching when extending partial types over finite
parameter sets. 

&nbsp;

**The full picture**

The connection that makes you question reality: These measure the exact same underlying property. Finite VC dimension corresponds EXACTLY to
the NIP property of formulas. The circle example works in both frameworks because they're detecting the same mathematical "tameness".
\\[\text{Finite VC dimension} \leftrightarrow \text{NIP}\\]

The same quality persists to learning under different assumptions. In PAC learning, we usually presume that the learner has access to all samples 
at once. In online learning, we assume that the learner gets sequential access to samples. This makes learning harder and there exists an 
expressiveness measure similar to VC dimension called the Littlestone dimension. One can show that 

\\[\text{Finite Littlestone dimension}\implies \text{Finite VC dimension} \\]

What is interesting is that finite Littlestone dimension corresponds exactly to stable formulas from the main gap theorem (a formula is 
stable if it was defined in a stable theory). Moreso, it is known that 

\\[\text{stable theories} = \text{NIP theories } \cap \text{ NSOP theories}\\]

where NSOP stands for "not the strong order property". In essence, it is another bound on the expressiveness of the theory, disallowing it from 
defining a strong order. From the machine learning perspective the strong order property is the only thing dividing online learnable classes from 
PAC learnable classes.

&nbsp;

Now, the full picture looks like this: 

\\[C_\varphi \text{ is PAC learnable } \iff \varphi \text{ is NIP     }\\]
\\[\Uparrow \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \Uparrow \\]
\\[C_\varphi \text{ is online learnable } \iff \varphi \text{ is stable }\\]

The deeper insight concerns branching behavior. Both perspectives 
rely on Sauer-Shelah type lemmas that control "shatter functions" -- essentially counting how many ways you can label finite sets.
Relationship suggests learning and logic are fundamentally about same thing: **extracting finite structure from infinite possibility spaces**.

&nbsp;

**Applications**

It is not often that we can actually speak about applications of model theory, where by applications we don't mean counting rational points on 
algebraic varieties, but rather concrete results that transform our understanding of learning itself. Model theorists spent decades classifying 
which theories are "tame" versus "wild," building a comprehensive taxonomy of mathematical structures.
The correspondence is precise and striking: any formula in a "tame" theory defines a learnable concept class, essentially for free. 

Examples of stable theories include:

&nbsp; - algebraically closed fields 

&nbsp; - differentially closed fields 

&nbsp; - elementary theory of non-abelian free groups

&nbsp; - various O-minimal theories

Examples of NIP theories include:

&nbsp; - All stable theories

&nbsp; - Real closed fields with ordering

&nbsp; - Valued fields (p-adics, etc.)

&nbsp; - Various geometric and arithmetic theories

Here's what is also interesting: the classification theory also tells us what's not learnable. Theories with the independence property 
correspond to concept classes with infinite VC dimension—provably not PAC learnable under standard assumptions.

&nbsp;

**Concrete examples**

&nbsp; - Threshold functions on $\mathbb{R}$: VC dimension 2, Littlestone dimension is infinite. Corresponds to definable sets in 
ordered structures. The threshold functions are not online learnable exactly because ordered structures trivially have strong order property, so 
one of the conditions "stable = NIP $$\cap$$ NSOP" is violated.

&nbsp; - Halfspaces in $\mathbb{R}^n$: VC dimension $n+1$, PAC learnable. Definable in real closed fields, thus stable.

&nbsp; - Neural networks with Pfaffian activations: Finite VC dimension, therefore learnable. Activations are definable in o-minimal 
structures—stable. Details in [VC dimension of Graph Neural Networks with Pfaffian activation functions](https://arxiv.org/abs/2401.12362).

&nbsp; - Boolean formulas: Can have infinite VC dimension, not generally PAC learnable unless you restrict the class. Correspond to unstable 
theories in model theory. Explains why learning arbitrary Boolean functions is hard.

&nbsp; - Elliptic curves over fields: Finite Littlestone dimension when definable in stable theories, online and PAC learnable.

This holds consistently. Every time model theorists prove something is stable, machine learning theorists gain new class of learnable concepts. 
It's like using [transfer principle](https://en.wikipedia.org/wiki/Transfer_principle#:~:text=In%20terms%20of%20model%20theory,for%20statements%20with%20bounded%20quantifiers) in non-standard analysis!

&nbsp;

**Conclusion**

In the end, learning theory and model theory accidentally discovered the same deep truth about pattern extraction. Decades of work in mathematical
logic just became catalog of learnable structures.

Sometimes mathematics reveals its most beautiful connections when fields unexpectedly collide—and sometimes those collisions reveal fundamental truths
about the nature of knowledge itself.

The connection between finite VC dimension and stability theory isn't just technical equivalence. It's pointing toward something deeper: A unified
understanding of when mathematical objects are "knowable" from finite information. And honestly, that's the kind of mathematical insight that keeps
you staring at the ceiling at 3am wondering about the fundamental nature of definability and learning.