- People:
	- Daniel Ostrow: SVP of technology 
		- Been at Clearpath for ~7 months
		- Took 1 year and 3 months off to parent
		- Intelerad as chief enterprise architect 2 years and 11 months
		- Ambra Health 11 years 6 months from inception to sale to Intelerad
	- Lauren Brown: President 
	- Jorey Chernet: CEO
	- Kamil Rahme: CTO

- Products:
	- PatientConnect
		- Built in AI and natural language processing (NLP) to translate rad reports into easy to understand 
		- The core product and likely their highest-volume module. It solves the CD fulfillment problem for the patient relationship.
		- How it works: Clearpath auto-queries PACS, RIS, and other systems to identify matching records when a patient submits a request through the portal, then fulfills digitally via email, a printable patient access page, or optionally burns a disc if a hard copy is still required.  Facilities can configure automated release rules by department or location, so imaging can go to the patient without staff intervention once the study is finalized.
		- On the patient side, patients can store records in Clearpath’s cloud platform and grant granular sharing permissions at the individual imaging study level to providers or family members. 
		- The disc burn option is notable. It’s a bridge for facilities that aren’t ready to go fully digital or have edge cases requiring physical media, without sacrificing the automated workflow for the majority of requests.
	- ProviderConnect
		- The provider-to-provider and referring physician module. It solves the care coordination gap when a patient is referred externally or seen across multiple facilities.
		- It transfers original-quality DICOM to any internal or external physician, regardless of whether they’re in-network or out-of-network, and can automatically retrieve and send prior studies when referring a patient to another provider or facility. 
		- The key differentiator here is the prior retrieval automation. Instead of a coordinator manually hunting for relevant priors before an appointment, Clearpath auto-retrieves relevant prior imaging studies before each appointment  based on configurable logic. That’s a real workflow problem in radiology that most facilities handle with manual labor or not at all.
		- The out-of-network capability matters too. Most PACS-native sharing tools are constrained to connected nodes. Clearpath’s value here is that the recipient doesn’t need to be on the same network or use the same PACS.
	- LegalConnect
		- The ROI module specifically for legal and insurance requestors. It solves the subpoena and third-party records request workflow, which is historically one of the most manual, paper-heavy processes in HIM.
		- It automatically queries and retrieves records from PACS, RIS, and billing systems in a single step, and handles payment collection at statutory limits or custom rates set by the facility. 
		- The payment collection piece is a meaningful operational differentiator. Legal ROI has historically involved mailing invoices, receiving checks, and reconciling manually. Clearpath owns that transaction digitally, which removes several steps and eliminates the accounts receivable lag.
		- One large imaging chain CEO noted it eliminated costs associated with subpoena delivery, status calls, checks in the mail, and disc burning while simultaneously increasing revenue , which reflects the dual-sided value: cost reduction and faster payment collection.
- Medical imaging flows:
	- Clearpath acts as DICOM node
	- Q/R: C-FIND to find studies and C-MOVE/C-GET to retrieve images
	- Surfaced for patients/providers

Broken link: https://www.myclearpath.com/solutions/patients

Questions to expect from him
The domain stuff will be light given he already knows your background. He’ll probably focus on:
	•	What have you been doing at SCHS and what does your day-to-day actually look like
		I started on the clinical side, moved into biomedical engineering, then spent time at Intelerad on the InteleShare platform doing pre-sales discovery and post-sales implementation. After that I moved to the health system side at SCHS to build out the SA function from scratch. I've now been the person doing vendor evaluations, architecture design, and stakeholder alignment on both sides of the table. This role is essentially the intersection of everything I've done.
	•	Why are you open to leaving a stable health system job
		I built the SA function there from the ground up and I'm proud of that work. But the trajectory I want, moving toward enterprise architecture and being part of something that's actively scaling, isn't available there in the near term. When I saw Clearpath and understood who was building it, it felt like the right time to make a move into a role where I can have real impact.
	•	Have you worked in a startup or early-stage environment before and how did you handle ambiguity
		That's basically been my entire role at SCHS. I was hired to build the SA function from scratch with no inherited framework, no templates, and no predecessor to hand things off from. I built the evaluation methodology, the SAD format, the stakeholder communication approach, all of it from zero. I'm comfortable operating without guardrails as long as I understand the outcome I'm working toward.
	•	What’s your experience with the sales side, are you comfortable in front of customers during pre-sales
		Yes, and I've been on both sides of it. At Intelerad I was in pre-sales conversations regularly, doing technical discovery, supporting demos, and helping shape the solution before anything was signed. I understood the sales motion and how to translate technical capability into something that resonated with clinical and IT stakeholders simultaneously.
		At SCHS I've been the customer in those conversations, which honestly made me better at the pre-sales side. I know what a health system IT team is actually evaluating, what questions they're not asking but should be, and where vendors tend to lose credibility. I can walk into a pre-sales conversation with a health system and speak their language authentically because I've sat in their chair.
	•	How do you approach an integration discovery with a customer IT team
		I start with the source systems before I talk about Clearpath. What PACS are they on, what version, how is it configured, who manages it. Same for RIS and EHR. I want to understand the existing data flows before I propose anything new, because integration problems almost always trace back to an assumption someone made early that nobody validated.
		From there I'm looking at three things: where does the data live, how does it move today, and where are the handoffs breaking down. Once I have that picture I can map Clearpath's integration touchpoints against their actual environment rather than a generic architecture diagram.
		I also pay attention to who's in the room. If it's just IT I'll go deeper on the technical layer. If clinical or operations is present I'll keep the integration conversation at the workflow level and translate the technical constraints into plain language. The discovery has to serve both audiences or you end up with a scoped solution that IT can build but nobody actually uses the way it was designed.
	•	What does a good SE function look like to you and how would you build it
		A good SE function is the connective tissue between sales, product, and the customer. Pre-sales it's making sure what gets sold is actually deliverable. Post-sales it's making sure what was delivered matches what was sold and feeding that gap analysis back into the product. At a company this size the SE also has to be a product expert internally, because engineering and customer success are going to lean on that seat when complex issues surface. I'd start by documenting the integration patterns I'm seeing repeatedly, build a discovery framework around Clearpath's most common customer environments, and create validation checklists that make go-live more repeatable as the team scales.
That last one is worth preparing for. At a 57-person company hiring an SE, he may be asking you to help define what the role becomes.

What you should ask him
On the business:
	•	How is Clearpath funded and are you profitable or what's the growth-stage
	•	What does the customer base look like today, health systems vs. imaging centers vs. both
	•	Where are the biggest integration friction points you’re hitting with customers right now
On the role:
	•	What does success look like in the first 90 days
	•	Is this the first dedicated SE hire or are you backfilling
	•	Who would I be working most closely with day to day
On growth:
	•	What’s the trajectory you’re building toward, another acquisition like Ambra or independent scale
	•	What does the SE function look like in two to three years if things go well

He probably won't say "tell me about your QA experience" directly. It'll come out as situational questions like:

- "Walk me through what happens after a contract is signed. How do you make sure the customer gets what was promised?"
- "Have you ever found a gap between what was scoped and what actually got delivered? How did you handle it?"
- "How do you validate that an integration is working correctly in a live environment?"
- "What does done look like to you on an implementation?"

What He’s Likely to Ask You

1. “Walk me through a complex integration you’ve worked on”

He’s looking for:

- Real systems: PACS, Epic
- Data flow clarity
- Where it broke and why

How to answer (structure it)

- Context (health system, systems involved)
- Architecture (how data moved)
- Problem (specific failure point)
- Resolution (what you did)
- Outcome (impact)

Avoid generic answers. Use one concrete example.

  

2. “How do you handle DICOM or imaging workflow issues?”

Expect depth:

- DICOM routing issues
- AE Titles, ports, firewall constraints
- C-FIND / C-MOVE failures

He’s checking:

- Have you debugged this in production?

  

3. “What typically goes wrong in imaging integrations?”

Strong answers include:

- Patient identity mismatches (MRN fragmentation)
- Study reconciliation issues
- Timing issues between RIS ↔ PACS ↔ EHR
- Vendor inconsistencies

This is a war story question.

  

4. “How do you approach solution design in a messy environment?”

He wants:

- Pragmatism over purity
- Tradeoff awareness

Good signals:

- Standardize where possible
- Adapt where necessary
- Minimize custom logic
- Design for failure

  

5. “How do you balance pre-sales vs delivery?”

This is a trap if answered poorly.

He’s looking for:

- You won’t overpromise
- You understand downstream impact

Strong angle:

I make sure what we design can actually be implemented in the customer’s environment, not just what demos well.

  

6. “How comfortable are you in ambiguity?”

This role is ambiguous. He knows it.

He wants:

- Ownership mindset
- Not waiting for perfect requirements

  

7. “Why this role vs staying in your current path?”

He’s testing:

- Are you intentionally moving toward product/SE hybrid work?
- Or just exploring randomly?

  

Questions You Should Ask Him (High Signal)

These should feel like peer-level architecture questions, not candidate questions.

  

1. Product Architecture

Where does Clearpath sit relative to PACS/VNA in your ideal architecture?

Follow-up:

Are you pulling via DICOM query/retrieve or relying more on routing into your platform?

This shows immediate depth.

  

2. Lessons from Ambra

Leverage his background:

Having built at Ambra Health, what problems do you think were left unsolved that you’re approaching differently here?

This is one of your highest-value questions.

  

3. Real Integration Pain

What are the most common failure points you see when integrating with customer environments?

You want him to talk about:

- Identity issues
- Workflow gaps
- System inconsistencies

  

4. Product Maturity

How standardized are your deployments vs custom per client?

This tells you:

- Scalability
- How much firefighting you’ll do

  

5. Scaling Challenges

What breaks today as you try to scale implementations?

This is a founder-level question. Very high signal.

  

6. Role Impact

Where do you need this role to make the biggest impact in the next 6 months?

Cuts through everything.

  

7. Technical vs Sales Tension

How do you ensure what’s sold aligns with what can actually be implemented?

This shows:

- You understand SE risk
- You won’t overcommit

  

What Will Impress Him

- Specific examples, not theory
- Understanding of failure modes
- Clear thinking about tradeoffs
- Comfort with imperfect systems
- Ability to connect workflow ↔ technical design

  

What Will Hurt You

- Generic “stakeholder communication” answers
- Buzzwords without depth
- Avoiding technical detail
- Overly polished “consulting-style” responses

  

Final Positioning

You want him thinking:

“This person has actually lived in the exact problems we’re solving and can operate without a playbook.”

  

Tactical Tip

Have 2–3 strong stories ready:

1. Complex imaging integration
2. A failure and recovery scenario
3. A cross-team (clinical + IT) alignment situation

If you deliver those well, the rest of the interview becomes a conversation, not an evaluation.