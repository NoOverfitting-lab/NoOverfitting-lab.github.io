---
layout: about
title: about
permalink: /
description:

profile:
  align: left
  image: Logo.jpg

news: false  # includes a list of news items
selected_papers: false # includes a list of papers marked as "selected={true}"
social: false  # includes social icons at the bottom of the page

---

## **No Overfitting Lab**

We are a group of independent researchers. We are working on the intersection of Natural Language Processing and Machine Learning and are focusing on:

<div style = "padding-left: 250px">
<ul>
  <li>Interpretability of big language models,</li>
  <li>Evaluation of NLP applications,</li>
  <li>Detection of mental health issues from text,</li>
  <li>Few-shot learning.</li>
</ul>
</div>

<hr>

<h3> News: </h3>

<div>
    <ul>
        {% for post in site.posts reversed %}
            <li>
                <b>[{{ post.date | date: "%B %Y" }}] </b>{{ post.description }}<a href="{{ post.url | prepend: site.baseurl | prepend: site.url }}">(Read more)</a>
            </li>
        {% endfor %}
    </ul>
</div>
