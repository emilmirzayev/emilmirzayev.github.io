---
title: "Sample Vega embedding"
date: 2018-05-29
excerpt: "Embedding Vega into blogpost"
barchart: "barchart.html"
gapminder: "gapminder.html"
---

This is a sample text


{% include {{ page.barchart }} %}

this is another sample text

this supposed to be an gapminder chart example

{% include {{ page.gapminder }} %}