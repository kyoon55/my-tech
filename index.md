---
layout: default
title: My Page
nav_order: 1
description: "Main Page."
---
 {% assign postdocs = site.pages | sort: 'date' | reverse %}

<style>
.container {
   border-bottom  : 1px solid black;
   padding-bottom : 1em;
}

.socialIconBack {
 color: $social-icons-background-color;
}

.socialIconTop {
 color: $social-icons-foreground-color;
}

.features {
 grid-area: c;
 display: flex;
 flex: 0 1 auto;
 align-content: flex-start;
 justify-content: flex-start;
 flex-grow: 1;
 flex-wrap: wrap;
 margin-top: 10px;
}

.feature {
 display: flex;
 padding-top: 20px;
 padding-left: 15px;
 padding-right: 15px;
 width: calc(100%/3);
}

.feature:nth-child(-n+3) {
    padding-top: 0px;
}

.feature:nth-child(3n) {
 padding-right: 0px;
}

.feature:nth-child(3n+1) {
 padding-left: 0px;
}

.featureText {
 margin-left: 18px;
}

@media only screen and (max-width: 992px) {

 .features {
  flex-grow: 1;
  flex-direction: row;
  flex-wrap: wrap;
  margin-top: 11px;
 }

 .feature {
  display: flex;
  padding-top: 41px;
  padding-left: 15px;
  padding-right: 15px;
  width: 100%;
 }

 .feature:nth-child(-n+3) {
  padding-top: 41px;
 }

 .feature:nth-child(1) {
  padding-top: 0px;
 }

 .feature:nth-child(3n) {
  padding-right: 15px;
 }

 .feature:nth-child(3n+1) {
  padding-left: 15px;
 }

}


</style>

<h2 style="text-align:center"> Welcome to my SRE Page </h2>
<div class="introduction">
<p style="text-align:center">I am an infrastructure and platform engineer with over 8 years of experience in the IT industry, catering to diverse clients. My primary responsibility revolves around ensuring the constant availability and reliability of the infrastructure that hosts our clients' IT solutions. Through extensive hands-on experience in managing IT infrastructure and platforms, I have consistently succeeded in maintaining a reliable and secure environment.</p>

<p style="text-align:center">To achieve my goal of delivering top-notch services, I am committed to staying updated with the latest technologies. I regularly conduct intensive research to incorporate cutting-edge solutions into our infrastructure. Moreover, I actively maintain a robust set of tools and resources that contribute to the dependability and efficiency of the systems.</p>

<p style="text-align:center">As a leader in my field, I not only strive for excellence in my work but also inspire and guide my team members to perform at their best. My passion for this profession drives me to constantly seek new challenges and opportunities for growth, ensuring that our clients receive the highest level of service and satisfaction.</p>
</div>

![](assets/images/architecture.png)
*From: Containerd: https://containerd.io/*

<h2 style="text-align:center"> My Duties and Responsibilities </h2>

<div class="features">
    <div class="feature">
        <div class="featureText">
            <h3>AWS</h3>
            <li>build Infrastructure on AWS</li>
      <li>Deploy several AWS resources and services</li>
      <li>Use CloudFormation to deploy and update resources</li>
        </div>
    </div>
    <div class="feature">
        <div class="featureText">
            <h3>Terraform</h3>
            <li>Build and update multi-plaform infrastructure using Terraform</li>
        </div>
    </div>
    <div class="feature">
        <div class="featureText">
            <h3>Jenkins</h3>
            <li>Run scheduled, triggered, or manual jobs on Jenkins</li>
   <li>Create and manage automated scripts inside Jenkins Jobs</li>
        </div>
    </div>
    <div class="feature">
        <div class="featureText">
            <h3>Kubernetes</h3>
            <li>Create, operate, and update Openshift and EKS distributions of Kubernetes</li>
   <li>Troubleshoot common issues of Kubernetes</li>
        </div>
    </div>
    <div class="feature">
        <div class="featureText">
            <h3>Performance Monitoring</h3>
            <li>Manage creation of logging, monitoring, and alerting tools such as Prometheus, Grafana, Splunk, etc.</li>
        </div>
    </div>
    <div class="feature">
        <div class="featureText">
            <h3>Docker</h3>
            <li>Create and operate images and containers that are quickly deployable.</li>
        </div>
    </div>
</div>

<h2 style="text-align:center"> Posts </h2>

{% for post in postdocs limit:8%}
 <div>
    {% if post.path contains "doc" %}
 <div class="container">
    <a class="post-link" href="/my-tech{{ post.url }}">{{ post.title }}</a>
   <p>
    {%- if post.description -%}{{ post.description }}{%- else -%}{{
    post.excerpt | strip_html }}{%- endif -%}
   </p>
   <p>{{ post.date | date_to_string }}</p>
    {% endif %}
 </div>
 </div>
{% endfor %}

{% include icons/icons.html %}