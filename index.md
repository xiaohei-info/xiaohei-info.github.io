---
layout: default
title: Home
---

<style>
.home-wrap { margin: 0 auto; }
.hero {
  padding: 1.65rem 0 1rem;
}
.eyebrow {
  display: inline-block;
  margin-bottom: 0.9rem;
  padding: 0.34rem 0.72rem;
  border-radius: 999px;
  font-size: 0.82rem;
  font-weight: 700;
  background: #eef2ff;
  color: #3730a3;
}
.hero h1 {
  margin: 0 0 0.8rem;
  font-size: 2.4rem;
  line-height: 1.16;
  letter-spacing: -0.02em;
}
.hero p {
  margin: 0;
  max-width: 760px;
  font-size: 1.04rem;
  line-height: 1.85;
  color: #4b5563;
}
.hero-actions {
  margin-top: 1.25rem;
  display: flex;
  gap: 0.8rem;
  flex-wrap: wrap;
}
.hero-actions a {
  display: inline-block;
  padding: 0.74rem 1rem;
  border-radius: 12px;
  text-decoration: none;
  font-weight: 700;
}
.hero-actions a.primary {
  background: #111827;
  color: #fff;
}
.hero-actions a.secondary {
  background: #f3f4f6;
  color: #111827;
}
.meta-strip {
  margin-top: 1rem;
  display: flex;
  gap: 0.6rem;
  flex-wrap: wrap;
}
.meta-strip span {
  display: inline-block;
  padding: 0.3rem 0.62rem;
  border-radius: 999px;
  background: #f3f4f6;
  color: #374151;
  font-size: 0.84rem;
}
.section {
  margin-top: 2.35rem;
}
.section h2 {
  margin-bottom: 0.8rem;
  font-size: 1.28rem;
}
.section-intro {
  margin: 0 0 1rem;
  color: #4b5563;
  line-height: 1.8;
}
.grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1rem;
}
.panel,
.post-card {
  border: 1px solid #e5e7eb;
  border-radius: 18px;
  background: linear-gradient(180deg, #ffffff 0%, #fafafa 100%);
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.05);
}
.panel {
  padding: 1rem 1.05rem;
}
.panel h3 {
  margin: 0 0 0.45rem;
  font-size: 1rem;
}
.panel p {
  margin: 0;
  color: #4b5563;
  line-height: 1.75;
}
.post-card {
  display: block;
  padding: 1.2rem 1.25rem;
  text-decoration: none;
  color: inherit;
}
.post-card:hover { text-decoration: none; }
.post-meta {
  margin-bottom: 0.55rem;
  font-size: 0.9rem;
  color: #6b7280;
}
.post-title {
  margin: 0 0 0.5rem;
  font-size: 1.24rem;
  line-height: 1.35;
  color: #111827;
  font-weight: 700;
}
.post-desc {
  margin: 0;
  color: #4b5563;
  line-height: 1.8;
}
.post-tags {
  margin-top: 0.9rem;
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}
.post-tags span {
  display: inline-block;
  padding: 0.26rem 0.55rem;
  border-radius: 999px;
  background: #f3f4f6;
  color: #374151;
  font-size: 0.82rem;
}
.mini-list {
  margin: 0;
  padding-left: 1.1rem;
  color: #4b5563;
  line-height: 1.9;
}
.small-note {
  margin-top: 0.85rem;
  font-size: 0.92rem;
  color: #6b7280;
  line-height: 1.75;
}
@media (max-width: 820px) {
  .hero h1 { font-size: 1.9rem; }
  .grid { grid-template-columns: 1fr; }
}
@media (prefers-color-scheme: dark) {
  .eyebrow {
    background: rgba(99, 102, 241, 0.18);
    color: #c7d2fe;
  }
  .hero p,
  .section-intro,
  .panel p,
  .post-meta,
  .post-desc,
  .mini-list,
  .small-note {
    color: #d1d5db;
  }
  .panel,
  .post-card {
    background: #111827;
    border-color: #374151;
    box-shadow: none;
  }
  .panel h3,
  .post-title,
  .section h2,
  .hero h1 {
    color: #f9fafb;
  }
  .hero-actions a.primary {
    background: #f9fafb;
    color: #111827;
  }
  .hero-actions a.secondary {
    background: #1f2937;
    color: #f9fafb;
  }
  .post-tags span,
  .meta-strip span {
    background: #1f2937;
    color: #e5e7eb;
  }
}
</style>

<div class="home-wrap">
  <section class="hero">
    <div class="eyebrow">Public Notes</div>
    <h1>xiaohei-info</h1>
    <p>
      一个面向公开读者的 AI Agent 协作与工程方法笔记站。这里主要记录多角色协作、工作流设计、交付机制和运行边界相关的设计文章，重点不是工具宣传，而是把方案如何设计、如何落地、为什么这样取舍讲清楚。
    </p>
    <div class="hero-actions">
      <a class="primary" href="/hermes-kanban-software-development-collaboration-design/">阅读最新文章</a>
      <a class="secondary" href="#about-site">了解这个站点</a>
    </div>
    <div class="meta-strip">
      <span>中文写作</span>
      <span>公开笔记</span>
      <span>持续更新</span>
    </div>
  </section>

  <section class="section">
    <h2>最新发布</h2>
    <p class="section-intro">
      当前公开的是一篇关于 Hermes 原生 Kanban 软件开发协作流程的设计文章，重点讨论角色边界、任务流转、验收契约，以及一套多角色协作方案是如何逐步收敛出来的。
    </p>
    <a class="post-card" href="/hermes-kanban-software-development-collaboration-design/">
      <div class="post-meta">设计文章 · 更新于 2026-05</div>
      <div class="post-title">Hermes Kanban 软件开发协作流程设计方案</div>
      <p class="post-desc">
        一篇围绕软件开发协作设计的长文，内容覆盖入口层与执行层解耦、orchestrator 的边界、PM / Architect / Reviewer / QA 的职责定义，以及如何让协作流程真正落到原生 Kanban contract 上。
      </p>
      <div class="post-tags">
        <span>AI Agent</span>
        <span>Workflow Design</span>
        <span>Kanban</span>
        <span>Software Delivery</span>
      </div>
    </a>
    <div class="small-note">目前站点以公开设计文章为主，后续会继续补充与 AI Agent 协作和工程化交付相关的内容。</div>
  </section>

  <section class="section" id="about-site">
    <h2>这个站点会写什么</h2>
    <div class="grid">
      <div class="panel">
        <h3>协作方案设计</h3>
        <p>关注多角色 AI 协作如何拆层、怎么定义边界、怎样避免流程重新塌回单点执行。</p>
      </div>
      <div class="panel">
        <h3>落地过程与取舍</h3>
        <p>不只展示结论，也解释为什么最终收敛成这版方案，哪些设计是为了提升可交付性和可维护性。</p>
      </div>
      <div class="panel">
        <h3>工程化运行视角</h3>
        <p>关注 workflow、验收契约、交接结构、运行边界这些更接近真实交付的问题，而不是只停留在概念层。</p>
      </div>
    </div>
  </section>

  <section class="section">
    <h2>适合谁看</h2>
    <ul class="mini-list">
      <li>在搭建 AI Agent 工作流或多角色协作链路的人</li>
      <li>关心软件开发流程如何和 AI 编排真正结合的人</li>
      <li>希望把“能跑起来”推进到“可交付、可维护、可复用”的团队或个人</li>
    </ul>
  </section>
</div>
