---
layout: post
author : Mechanical Object
category: Article
title: Educating business people
tagline: 
tags: [business requirements, project, specifications]
published : true
---
When you are working as developer on a project, there is a great probability that you'll work with fellows that we call **business people**. Basically these folks claim to know business aspects of the domain, they are in relation with frontend users and they give functional specifications to the developer. Before developing my ideas, let me be clear. 

<!--more-->

I don't expect anything from business people regarding :

*   the way they formulate their demand _-which most of the time is incomprehensible by a developer_-.
*   technical limitations we may encounter on the demand they make

But I expect them **to know the business** and when they make a demand I expect them **to calculate business impacts** and give complete overview of what will change. Let me give an example. In my current project, we are doing a brand new P&L system for a worldwide corporate and investment bank. **I cannot give the exact scenario due to confidentiality agreement** I signed but I assure you that the example I give below is a a pretty good projection of the discussion I had last week.

# Example

Context : Our business is to sell complete buildings. One building is composed of multiple flats and to find the price of a building , you sum the prices of the flats in this building. 

* **Business fellow :** Hey, we want to change the formula that defines the price of a flat. 
* **Me :** Ok, no problem, what is the new formula ? 
* **Business fellow :** Before the formula was 

{% raw %}
price of the flat = neighbourhood coefficient * surface of the flat * unit price`
{% endraw %}

 Now we want it to be 

{% raw %}
price of the flat = neighbourhood coefficient * surface of the flat * unit price * level coefficient for the flat
{% endraw %}

 * **Me :** Ok, is this all regarding the change ?
 * **Business fellow :** Yes, that is all we want to change the other calculations are fine. 

 So I am looking through the code to give an estimate of how much the development will cost. Just one change in the formula, reorder unit tests, adapt integration test. That should not be big deal. 

 While reading the code, I realize that price of the flat is also used in other formulas , like the tax projection formula that depends on the price of a flat. But wait, there is a problem here, because business fellow just confirmed me that all the other results were fine. Hmm, I'll send another mail to confirm the point.

{% raw %}
Hello,

In our previous exchange, you confirmed me that all the other results were fine. Have you looked at the tax 
projection results ? Are they fine too ? Because the modification you ask will have impacts on this result.

Thanks

{% endraw %}


After a few minutes, business fellow replies in single line, by CCing other business people :

{% raw %}

Yes, they are fine and the other results should not change.

{% endraw %}

I am confused, I rewrite a e-mail.

{% raw %}

Hello business fellow,

the tax projection formula is like the following :

tax projection = price of a flat * forex rate GBP/EUR * tax rate 

Either this formula we are using is wrong or the change you ask will impact this and probably other results too.
Could you clarify this point and give a complete review of all business impacts please?
Thanks

{% endraw %}


Than the business fellow replies

{% raw %}

Tax projection formula is correct and thus it will be impacted 

{% endraw %}


**Is this a joke?** What is the purpose of your position if you reply to me like that ? I am not angry, I am just disappointed. The software development is a team effort. Everyone should play ball nicely. 

So very kind note to business people : Preparing requirements, calculating business impacts of a change in the requirements is **your job**! More I work on the project, more I'll get to know the business, I can spot missing points on your requirements and kindly report these to you. But let’s be clear, calculating business impacts is not my job! I know how to develop, you know the business, am I right ? If I do you job too, why are you receiving a salary and occupy this position ?

# Important note

Please do not take this post as a generalized feeling. In my career, I had the chance to meet really competent business people and I have learnt a lot from them. As you know there are always rotten eggs, we have them among developers, business people as well in other positions.