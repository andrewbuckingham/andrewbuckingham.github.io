---
title: Horizon
tags:
categories:
  - software-development
date: 2024-02-10 20:39:30
---
# Why should you care?

Post Office Horizon. Another failed £1bn government IT project. So what? OK it's a waste of money, but _plus ça change_?

Here's why.

Because this failure is associated with:

- 4,000 people falsely accused of fraud or mismanagement
- 700 people prosecuted
- 200 people sent to prison
- 3 people's deaths

I use the word "people" 4 times, deliberately. Because this is about _people_. The people affected; and the people responsible.

# What's Horizon?

Horizon is a £1bn IT system enabling UK Government departments to make social security payments. When development began in 1996, it was designed for the UK Government benefits agency and the state-owned Post Office.

Post Offices are run in a franchise-like way by "Sub Postmasters" (**SPMs**). These are _people_ who ran their Post Offices quietly and successfully for many years prior to Horizon.

# Problems

When Horizon was rolled out to Sub Postmasters they began noticing financial errors. Money was going missing.

Important context: Horizon had already "failure[d] to meet the first milestone [which] cannot but cause concern in a project with such a chequered history". This is a quote from the Parliamentary Select Committee report[^1] in 1999. The same report tells us the UK benefits agency had pulled out of the project by 1999, leaving Post Office as sole client (and financier) of Horizon.

Horizon as a project was already on the back foot, reputationally and financially, with Post Office now the *de-facto* responsible party. Rather than investigating their new IT system for financial errors, Post Office robustly turned the problem back on the Sub Postmasters; requiring them to make-good the shortfalls without opportunity for dispute (Hamilton v Post Office, judgement, 2021)[^2].

If SPMs didn't repay the shortfalls, Post Office auditors brought prosecutions against them. They were wrongfully pressured (Fraser J, Judgment No 3)[^3] into admitting charges of mismanagement, under the threat that they would otherwise risk prison if they denied wrongdoing.

Many lost their homes due to the incurred debt. Others went to prison. Some died.

# What can you do about this?

Help share this knowledge in some small way. Play your part in improving the collective knowledge of society, to prevent a similar disaster happening again.

Collective knowledge works. It's how society is beginning to scrutinise institutions on climate action. Societal knowledge leads to well-informed scrutiny.

Read on, and I'll describe 5 failures. I'll separate out the technical detail so you can skip over those bits if they're not of interest. The rest is a human story.

# Failure #1: Transparency and disclosure

When SPMs called the Horizon support helpline to report errors, Fujitsu staff recorded them into a "Known Error Log". This would have been useful as evidence in Court. But these errors were not widely disclosed by Post Office during any of its legal proceedings against SPMs. They maintained that Horizon was robust, without providing evidence.

The next failure was during counter claims by SPMs against Post Office. The Government and Members of Parliament instigated independent inquiries; but Post Office aggressively withheld evidence[^9] in an attempt to protect from reputational damage[reference trusted brand]. Prosecutions continued.

In all these legal cases transparency would have enabled the focus to be put back onto Horizon's bugs, and innocent people kept out of prison.

# Failure #2: Acquisition due-diligence

The media has reported Fujitsu as the developer of Horizon, and much of the public blame is directed at them.

Fujitsu is unimaginably huge. Annually they generate net income of $1.2bn, from $22bn revenue. Most of the public hadn't heard of them - until now, when they've first learned of Fujitsu as the developers of a £1bn failed IT system that has ruined thousands of lives.

But, Fujitsu are subject to reputational and financial penalty for an IT system they didn't directly create.

Horizon was actually developed by ICL, which originated from the ashes of the 1960s British computer industry. Fujitsu wholly acquired ICL in 1998, by which time Horizon was already a failing project and subject to Parliamentary review.

Fujitsu didn't adequately assess the Horizon IT project before completing the ICL acquisition. This due diligence failure is presenting itself now, 25 years later, as a possible $1bn financial liability and incalculable reputational damage.

There's an additional irony here, post-acquisition, when Horizon continued as an ICL branded project. There's suggestion that the emerging poor reputation of Horizon had damaged the ICL brand, contributing to Fujitsu ending the ICL brand name and Horizon becoming a Fujitsu-branded product. Fujitsu mortgaged its own reputation on the failing of ICL.

# Failure #3: Software DevOps

Software DevOps is an industry term that includes how software is rolled out to the users, and how it's supported and maintained by technical staff.

An important principle of DevOps is that technical staff should never have unrestricted access to the data in a live system. But in Horizon it was routine[^3] for support staff to "fix" data errors in SPM's accounts, without any proper controls. And worse, without proper logs of what they'd changed.

Good DevOps practices are also closely aligned with how a system is tested. There was very little evidence in the Court expert witness statements of anything that a modern software developer would consider "good". For the minimal system errors that Post Office reported, there was no focus on how the fixes were formally tested.

Another principle is "observability and monitoring" of a system: it should continuously and thoroughly log data about how well it's functioning. Court proceedings have subsequently showed there wasn't enough logging for fault to be attributed correctly.

### Technical thoughts

- It's dangerous, directly editing data in a production system. Especially the methods that Horizon staff were using, which was to manipulate [reference] the data using SQL CLI commands; bypassing many of the checks and balances that written into the software. Privileged User Access by support staff was widespread used as a regular method of solving application issues, and without logging of what actions were taken. There was a Transaction Correction Tool script but its usage was not logged.[^6]

- Errors in data are almost always due to a software problem further upstream. By simply fixing the data, it takes the focus away from helping to identify the root-cause. Even worse, it can destroy key information that would help the bug to be properly investigated.

- Testing is vital. Horizon was in development for over a decade, with many changes to add new features and fix bugs. Whenever part of a system is modified, there needs to be "regression testing" of the parts of the system that sit around that. This might be a combination of manual test plans; synthetic test data; automated test scripts... None of this was mentioned in the Court proceedings that I've seen.

# Failure #4: Software and Integration

The name "Horizon" is an umbrella covering many different individual software systems, all developed by different teams. Bringing systems together in harmony is called "Integration".

Much of the Court evidence given by the Post Office expert witness[^8] was on the robustness and reliability of just the _database_ system within Horizon. And while his technical description of database systems is true, I'd suggest it's not useful. It focused attention away from where most software bugs occur: in the software. Not the database.

The expert witness didn't offer expertise on the challenges (and scope for error) arising from how the systems are integrated, or from the software algorithms that sit above the database. 

### Technical thoughts

- The expert witness talked at length about the inherent safety of double-entry bookkeeping in accountancy, and how this forms the basis of Horizon's claimed robustness. He went into great detail explaining how relational databases work, including referential integrity and transaction blocks, and that this is proof of robustness.

- One of the proven errors in the pivotal Bates v Post Office was a bug when the same SPM user logged on to two computer terminals at the same time. This is a classic "integration testing" failure. The software was never built to cope with the same user logging on twice, because (in theory) it wasn't possible. The complexity of the two systems together - the computer terminal and the Horizon system - caused the error.

# Failure #5: Legal

The early prosecutions of SPMs relied on a (then legally valid) assumption that:

> computer[s] operate correctly unless there is explicit evidence to the contrary.
*Law Commission Presumption, 1997*

Put another way, the SPMs had no defence because Horizon was legally presumed to be functioning correctly. SPMs could claim there was a bug, but without any good error logging or relevant testing (see Failure #3) there was literally no evidence to the contrary. SPMs were found guilty and prosecuted.

The legal system's presumption of computer systems was simplistic, and not aligned with the reality of large-scale software systems. Thankfully, this presumption has now been recognised by a Law Commission article[^4] as not "reflect[ing] the reality of general software-based system behaviour."

Citing this article, Parliamentary Justice Committee evidence[^5] has recommended mandatory training for all lawyers on the complexities of software development.

Much of that improved legal thinking comes from a landmark legal case; one where SPMs received justice for the first time. This is "Bates v Post Office"[^3] presided over by Justice Fraser, examining 29 bugs[^7] that had been involved in falsely prosecuting SPMs. Justice Fraser's analysis is highly astute. One of his findings states it was extremely difficult for the experts to make categorical negative statements of the form "X or Y never happened"; and even where there is some evidence then it would be too complex to disentangle. Perhaps his most damning assessment is:

> This approach by the Post Office has amounted, in reality, to bare assertions and denials that ignore what has actually occurred, at least so far as the witnesses called before me in the Horizon Issues trial are concerned. It amounts to the 21st century equivalent of maintaining that the earth is flat.
*Bates v Post Office, judgment No 6, paragraph 929*[^6]

I recommend you read a few pages of Justice Fraser's findings. It's a rare uplifting part of the Horizon story.

### Technical thoughts

- It's almost incomprehensible that the expert witness for the Post Office portrayed software as inherently robust. *Any* software professional will tell you this is never the case. You need to write test cases to test the code for bugs. Anything not covered by a well-defined test case cannot be claimed as bug-free or robust.

- Modern software development places huge emphasis on testing and monitoring. When errors occur, the system should have logged enough data that the software team can _reproduce_ the bug by testing for it in a controlled environment. Then they modify the software to fix it, and then re-test to validate the bug goes away. Horizon didn't follow this.

- We think of making software "observable and monitorable" because, as software professionals, it makes our lives easier. We can catch problems early; and we can find bugs more easily. But since reading about Horizon, I realise that if a software vendor ends up in Court, they need to have evidence (e.g. system logs) to prove culpability.

- There was insufficient system logging to determine cause of errors, let alone to determine it was the fault of SPMs. Technical support staff created standard code scripts and tooling for fixing errors that happened often, but even these tools didn't log that they had been used.

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

# Further reading

Judiciary reports from Bates v Post Office: https://www.judiciary.uk/judgments/bates-others-v-post-office/

[^1]: https://publications.parliament.uk/pa/cm199900/cmselect/cmtrdind/50/5004.htm (UK Parliamentary Select Committee report, 1999)
[^2]: https://www.judiciary.uk/wp-content/uploads/2022/07/Hamilton-Others-v-Post-Office-judgment-230421.pdf (Hamilton v Post Office, judgment, 2021)
[^3]: https://www.bailii.org/ew/cases/EWHC/QB/2019/606.pdf (Bates v Post Office, judgment No 3 Common Issues, 2019)
[^4]: https://journals.sas.ac.uk/deeslr/article/view/5143/5027 (Law Commission article advising changes to presumption on reliability of computer systems, 2020)
[^5]: https://committees.parliament.uk/writtenevidence/7839/pdf/ (Parliamentary Justice Commission evidence recommending mandatory software awareness training in the justice system)
[^6]: https://www.judiciary.uk/wp-content/uploads/2019/12/bates-v-post-office-judgment.pdf (Bates v Post Office, judgment No 6 Horizon Issues, 2019)
[^7]: https://www.judiciary.uk/wp-content/uploads/2022/07/bates-v-post-office-appendix-2-1.pdf (Bates v Post Office, judgment No 6 Summary of bugs, 2019)
[^8]: https://www.judiciary.uk/wp-content/uploads/2022/07/bates-v-post-office-appendix-1-1.pdf (Bates v Post Office, judgment No 6 Technical appendix, 2019)
[^9]: https://committees.parliament.uk/writtenevidence/6580/html/ (Parliamentary Select Committee evidence, 2020)
