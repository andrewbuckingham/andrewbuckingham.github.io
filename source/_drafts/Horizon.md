---
title: Horizon
tags:
categories:
  - software-development
date: 2024-02-10 20:39:30
---
# Why should you care?

Horizon. Another failed £1bn government IT project. So what? OK it's a waste of money, but _plus ça change_?

Here's why.

Because this failure is associated with:

- 4,000 people falsely accused of fraud or mismanagement
- 700 people prosecuted
- 200 people sent to prison
- 3 people's death

I use the word "people" 4 times, deliberately. Because this is about _people_. The people affected; and the people responsible.

I'm usually incredibly proud of being in the software industry. I believe in it a force for good, helping people achieve more and live more comfortably. But when discussing Horizon with a colleague, he asked: "is this our industry's Grenfell?"

# What's Horizon?

Horizon is a £1bn IT system developed for UK Government departments to computerise social security payments. When development began in 1996, it was intended for use by the UK Government benefits agency and the state-owned Post Office.

Post Offices are run in a franchise-like way by "Sub Postmasters" (**SPMs**). These are _people_ who ran their Post Offices quietly and successfully for many years prior to Horizon.

# Problems

When Horizon was rolled out to Sub Postmasters they began noticing financial errors. Money was going missing.

Rather than investigating the IT system, Post Office robustly turned the problem back on the Sub Postmasters, requiring them to firstly make-good the shortfalls without opportunity for dispute [Hamilton Others v Post Office, 2021].

When shortfalls were later escalated to Post Office auditors, SPMs were routinely prosecuted. They were wrongfully pressured [Fraser J, Judgment No 3] into admitting charges of mismanagement and repaying the "missing" money, under the threat that they would otherwise risk prison if they denied wrongdoing.

Many lost their homes due to the incurred debt. Others went to prison. Some died.

# What can you do about this?

Help share this knowledge in some small way. Help us play our part in improving the collective knowledge of society, to prevent a similar disaster happening again.

Collective knowledge works. It's how society is beginning to scrutinise institutions on climate action. Societal knowledge leads to well-informed scrutiny.

Read on, and I'll describe 5 failures. If there's technical detail, I'll separate it out so you can skip over those bits if they're not of interest. The rest is a human story.

# Failure #1: Transparency and disclosure

When SPMs called the Horizon support helpline to report errors, the Fujitsu staff recorded them into a "Known Error Log". This would have been useful as evidence in Court. But these errors were not widely disclosed by Post Office during any of its legal proceedings against SPMs. They maintained that Horizon was robust, without providing evidence.

The next failure was during counter claims by SPMs against Post Office. The Government and Members of Parliament instigated independent inquiries; but Post Office aggressively redacted evidence. Prosecutions continued.

In all these legal cases, transparency would have enabled the focus to be put back onto Horizon's bugs, and innocent people kept out of prison.

# Failure #2: Acquisition due-diligence

The media has reported Fujitsu as the developer of Horizon, and much of the public blame is directed at them.

Fujitsu is unimaginably huge. Annually they generate net income of $1.2bn, from $22bn revenue. Most of the public hadn't heard of them - until now, when they've first learned of Fujitsu as the developers of a £1bn failed IT system that has ruined thousands of lives.

But, Fujitsu are subject to reputational and financial penalty for an IT system they didn't directly create.

Horizon was actually developed by ICL, which originated from the ashes of the 1960s British computer industry. Fujitsu wholly acquired ICL in 1998, by which time Horizon was already a failing project and subject to Parliamentary review.

Fujitsu didn't adequately assess the Horizon IT project before completing the ICL acquisition. This due diligence failure is presenting itself now, 25 years later, as a possible $1bn financial liability and incalculable reputational damage.

There's an additional irony here, post-acquisition, when Horizon continued as an ICL branded project. There's suggestion that the emerging poor reputation of Horizon had damaged the ICL brand, contributing to Fujitsu ending the ICL brand name and Horizon becoming a Fujitsu-branded product. Fujitsu mortgaged its own reputation on the failing of ICL.

# Failure #3: Software DevOps

Software DevOps is an industry term that includes how software is rolled out to the users, and how it's supported and maintained by technical staff.

An important principle of DevOps is that technical staff should never have unrestricted access to the data in a live system. But in Horizon, it was routine [cite] for support staff to "fix" data errors in SPM's accounts, without any proper controls, and without proper logs of what they'd changed.

Good DevOps practices are also closely aligned with how a system is tested. There was very little evidence in the Court expert witness statements of anything that a modern software developer would consider as "good". For the few system errors that Post Office reported, there was never mention of how the fixes were formally tested.

Another DevOps principle is "observability and monitoring" of a system. This means it should constantly and thoroughly collect data about how well it's functioning. The technical term is "logging" - it's the system keeping a log of everything that happens, so that any problems or errors can be accurately investigated. Court proceedings have subsequently showed there wasn't enough logging for fault to be attributed correctly.

### Technical details

- It's dangerous, directly editing data in a production system. Especially the methods that Horizon staff were using, which was to directly manipulate [reference] the data using SQL CLI commands; bypassing most of the checks and balances that written into the software.

- Errors in data are almost always due to a software problem further upstream. By simply fixing the data, it takes the focus away from helping to identify the root-cause. Even worse, it can destroy key information that would help the bug to be properly investigated.

- Testing is vital. Horizon was in development for over a decade, including adding new features and fixing bugs. Whenever part of a system is modified, there needs to be "regression testing" of the parts of the system that sit around that. This might be a combination of manual test plans; synthetic test data; automated test scripts... None of this was mentioned in the Court proceedings that I've seen. I suspect testing was inadequate. Systems cannot be claimed to be robust until they've been tested. The presumption should always be that a system is _not_ robust [reference], unless it can be proven otherwise.

# Failure #4: Systems Integration

The name "Horizon" is an umbrella covering many different individual systems, all developed by different teams. Bringing systems together in harmony is known as "Integration".

Much of the Court evidence given by the Post Office expert witness was focused on the robustness and reliability of the individual systems within Horizon. The expert witness didn't offer expertise on the challenges (and possibility of significant errors) arising from how the systems are integrated.

### Technical details

- The expert witness talked at length about the inherent safety of double-entry bookkeeping in accountancy, and how this forms the basis of Horizon's claimed robustness. He went into great detail explaining how relational databases work, including referential integrity and transaction blocks, and that this is proof of robustness. And while his technical description of database systems is true, I'd suggest it's not useful. It focused the inquiry's attention away from where most software bugs occur: in the software. Not the database. Most software developers know this, so it's upsetting that an expert witness didn't express it.

# Failure #5: Legal

The early prosecutions of SPMs relied on a (then legally valid) assumption that:

> a computer operated correctly unless there is explicit evidence to the contrary "Law Commission Presumption, 1997" [reference]

Put another way, the SPMs had no defence because Horizon was legally presumed to be functioning correctly. SPMs could claim there was a bug, but without any good error logging or relevant testing (see Failure #3) there was literally no evidence to the contrary. SPMs were found guilty and prosecuted.

The legal system's view of computer systems was simplistic, and not sufficiently aligned with the reality of large-scale software systems.

Thankfully - if that's possible to say - this presumption has now been recognised by a Law Commission article as not "reflect[ing] the reality of general software-based system behaviour" [reference]

In other words, the legal profession has since realised that software systems are too complex to simply assume they function correctly, and that evidence is always needed. The Horizon failure was a central theme of the Law Commission's article.

A lot of that improved legal thinking is down to a landmark case in the Horizon story; one where the tide finally turned and SPMs received justice for the first time. This is the case of "Bates v Post Office" which was presided over by Justice Fraser. His highly astute analysis helped to break through the limited and confusing evidence of prior cases. I recommend you read a few pages, as it's the only uplifting part of the Horizon story.

### Technical

- Modern software development places huge emphasis on testing and monitoring. When errors occur, the system should have logged enough data that the software team can _reproduce_ the bug by testing for it in a controlled environment. Then they modify the software to fix it, and then re-test to validate the bug goes away. Horizon didn't follow this.

- We think of making software "observable and monitorable" because, as software professionals, it makes our lives easier. We can catch problems early; and we can find bugs more easily. But since reading about Horizon, I realise that if a software vendor ends up in Court, they need to have evidence (e.g. system logs) to prove culpability.

# Ivory Towers

I need to put my hand up and admit it's easy to criticise.

In my corner of the software industry, I'm incredibly fortunate to work directly with client organisations. I _feel_ the problems first-hand, because I'm working almost as an extension of the client organisation. This makes it easier to see the organisation as _people_ and to become invested in any problems they're experiencing.

But typical of a large system, Horizon's software professionals and technical support were far removed from the client at a human level. This is why it's vital to follow good software development practices, and to _educate_ the software professionals so they understand the benefits.

I'm also lucky that I get the opportunity to learn and apply the latest thinking in software development practices. I'm not talking about new technologies necessarily (although I'm lucky I get to do that too). Rather, I'm talking about the human practices around how to successfully develop software. With a system like Horizon, which has a decades-long gestation, development practices are often stuck in the past too. It's harder to keep evolving the culture.

Good software development is equally about human culture and practices, as it is about technology.

Maybe even more so.

# Timeline

To round this off, I thought I'd try illustrating the sheer size of the Horizon project by showing how long it took to build... And how long Sub Postmasters waited for justice.

{% mermaid %}
%%{init: { 'logLevel': 'debug', 'theme': 'base', 'gitGraph': {'showBranches': true, 'showCommitLabel': true, 'mainBranchName': 'Government', 'mainBranchOrder': 0}} }%%

    gitGraph

    commit id: "Procurement (1995)"

    branch ICL order: 2

    checkout Government
    branch Benefits_Agency

    branch Post_Office
    commit id: "Trial roll-out (1995)"
    commit id: "Bugs reported (1995)"
    commit id: "First prosecutions (1996)"

    checkout ICL
    commit id: "Fujitsu acquire ICL (1998)"

    checkout Post_Office
    commit id: "National roll-out (1999)"

    checkout Benefits_Agency
    commit id: "Cancelled (1999)"

    checkout Government
    merge Benefits_Agency
    commit id: "Parliamentary review (1999)"

    checkout Post_Office
    commit id: "Widespread prosecutions of SPMs (1999)"

    checkout ICL

    branch Fujitsu order: 3
    commit id: "ICL rebrand as Fujitsu (2001)"
    commit id: "Full roll-out (2001)"

    checkout Post_Office
    commit id: "Media reports SPM mistreatment (2009)"
    commit id: "Parliamentary orders a review (2012)"
    commit id: "Review cancelled before publication (2015)"
    commit id: "SPMs bring legal action (2017)"
{% endmermaid %}

Notes:

May 1996 awarded to ICL
1998 Fujitsu became sole owner (increasing its existing shareholding)
May 1999 DSS withdrew, leaving just Post Office
1999-2001 wide scale rollout

1999-2015 4,000 accusations, 700 convictions and 236 prison

ICL brand replaced June 2001: https://www.theregister.com/2001/06/21/icl_brand_put/

Second Sight's first interim report: https://www.jfsa.org.uk/uploads/5/4/3/1/54312921/pol_interim_report_signed.pdf

Parliament report recommending restructing of Horizon project, 1999: https://publications.parliament.uk/pa/cm199900/cmselect/cmtrdind/50/5004.htm

Second Sight not given access to evidence: https://committees.parliament.uk/writtenevidence/6580/html/

- Select Committee evidence on Second Sight not being allowed to gather evidence
- "plea due to computer not working will not be accepted (2007)"

Bates v Post Office, Fraser J, judgment no 3: https://en.wikipedia.org/wiki/Bates_v_Post_Office_Ltd_(No_3)

https://www.judiciary.uk/wp-content/uploads/2022/07/Hamilton-Others-v-Post-Office-judgment-230421.pdf

- Possible to directly manipulate the database by executing SQL commands directy from a command line interface.
- Extremely difficult for the experts to make categorical negative statements of the form "X or Y never happened" due to the complexity of tooling used for remediating data issues; and either there will be no evidence or it will be too complex to disentangle:
- Privileged User Access by support staff was widespread used as a regular method of solving application issues, and without logging of what actions were taken. There was a Transaction Correction Tool script but its usage was not logged.
  https://www.judiciary.uk/wp-content/uploads/2019/12/bates-v-post-office-judgment.pdf

https://www.jfsa.org.uk/uploads/5/4/3/1/54312921/an_introduction_to_the_ci_and_hi_judgments.pdf

Horizon architecture description:
https://www.judiciary.uk/wp-content/uploads/2022/07/bates-v-post-office-appendix-1-1.pdf

Law Commission article on the presumption of reliability of computer systems

- Dr Worden over emphasising RDBMS transactions:
  https://www.judiciary.uk/wp-content/uploads/2022/07/bates-v-post-office-appendix-1-1.pdf

Judgment No 6 Bates:
https://www.judiciary.uk/wp-content/uploads/2019/12/bates-v-post-office-judgment.pdf

Judgment No 6 Bates Appendix 2 Summary of Bugs, Errors, Defects:
https://www.judiciary.uk/wp-content/uploads/2022/07/bates-v-post-office-appendix-2-1.pdf

https://www.judiciary.uk/judgments/bates-others-v-post-office/

Limitations of the LC Presumption (computers are correct unless to the contrary)
https://journals.sas.ac.uk/deeslr/article/view/5143/5027
