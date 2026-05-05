---
layout: default
title: xiaohei-info
---

<style>
.home-wrap {
  margin: 0 auto;
}
.hero {
  padding: 1.5rem 0 0.75rem;
}
.hero-badge {
  display: inline-block;
  margin-bottom: 0.85rem;
  padding: 0.32rem 0.7rem;
  border-radius: 999px;
  font-size: 0.82rem;
  font-weight: 600;
  background: #eef2ff;
  color: #3730a3;
}
.hero h1 {
  margin: 0 0 0.85rem;
  font-size: 2.2rem;
  line-height: 1.2;
}
.hero p {
  margin: 0;
  font-size: 1.02rem;
  line-height: 1.8;
  color: #4b5563;
  max-width: 760px;
}
.hero-actions {
  margin-top: 1.2rem;
  display: flex;
  gap: 0.8rem;
  flex-wrap: wrap;
}
.hero-actions a {
  display: inline-block;
  padding: 0.72rem 1rem;
  border-radius: 12px;
  text-decoration: none;
  font-weight: 600;
}
.hero-actions a.primary {
  background: #111827;
  color: #fff;
}
.hero-actions a.secondary {
  background: #f3f4f6;
  color: #111827;
}
.section {
  margin-top: 2rem;
}
.section h2 {
  margin-bottom: 0.9rem;
  font-size: 1.25rem;
}
.doc-card {
  display: block;
  text-decoration: none;
  color: inherit;
  border: 1px solid #e5e7eb;
  border-radius: 18px;
  padding: 1.2rem 1.25rem;
  background: linear-gradient(180deg, #ffffff 0%, #fafafa 100%);
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.06);
}
.doc-card:hover {
  text-decoration: none;
}
.doc-card-title {
  margin: 0 0 0.45rem;
  font-size: 1.2rem;
  font-weight: 700;
  color: #111827;
}
.doc-card-desc {
  margin: 0;
  line-height: 1.75;
  color: #4b5563;
}
.doc-meta {
  margin-top: 0.9rem;
  font-size: 0.92rem;
  color: #6b7280;
}
.grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1rem;
}
.note {
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  padding: 1rem;
  background: #fff;
}
.note h3 {
  margin: 0 0 0.45rem;
  font-size: 1rem;
}
.note p {
  margin: 0;
  color: #4b5563;
  line-height: 1.7;
}
@media (max-width: 780px) {
  .hero h1 {
    font-size: 1.75rem;
  }
  .grid {
    grid-template-columns: 1fr;
  }
}
@media (prefers-color-scheme: dark) {
  .hero-badge {
    background: rgba(99, 102, 241, 0.18);
    color: #c7d2fe;
  }
  .hero p,
  .doc-card-desc,
  .note p,
  .doc-meta {
    color: #d1d5db;
  }
  .doc-card,
  .note {
    background: #111827;
    border-color: #374151;
    box-shadow: none;
  }
  .doc-card-title,
  .note h3 {
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
}
</style>

<div class="home-wrap">
  <section class="hero">
    <div class="hero-badge">Published Design Document</div>
    <h1>xiaohei-info 文档页</h1>
    <p>
      这里放的是已经整理完成、适合公开阅读的设计文档。当前首页收录的是一篇关于 Hermes 原生 Kanban 软件开发协作流程的完整设计方案，重点讨论角色边界、运行架构、任务流转和验收契约。
    </p>
    <div class="hero-actions">
      <a class="primary" href="/hermes-kanban-software-development-collaboration-design/">阅读设计文档</a>
      <a class="secondary" href="#featured-docs">查看文档简介</a>
    </div>
  </section>

  <section class="section" id="featured-docs">
    <h2>当前发布</h2>
    <a class="doc-card" href="/hermes-kanban-software-development-collaboration-design/">
      <div class="doc-card-title">Hermes Kanban 软件开发协作流程设计方案</div>
      <p class="doc-card-desc">
        一份面向真实运行的协作设计文档。内容覆盖入口层与执行层解耦、orchestrator 的职责边界、PM / Architect / Reviewer / QA 的角色定义，以及如何让 Hermes Kanban 成为唯一 runtime contract。
      </p>
      <div class="doc-meta">适合阅读对象：软件开发协作流程设计、AI Agent 编排、Hermes Kanban 使用者</div>
    </a>
  </section>

  <section class="section">
    <h2>这篇文档重点讲什么</h2>
    <div class="grid">
      <div class="note">
        <h3>角色怎么分</h3>
        <p>把 default / trading、orchestrator、PM、Architect、工程实现、Reviewer、QA 的职责边界拆清楚，避免重新塌回单点超人模式。</p>
      </div>
      <div class="note">
        <h3>流程怎么跑</h3>
        <p>说明根任务如何进入看板、任务图如何拆解、不同复杂度的任务该走长链路还是短链路，以及 handoff 要留下哪些信息。</p>
      </div>
      <div class="note">
        <h3>运行怎么收敛</h3>
        <p>解释为什么只保留一个主 dispatcher host、为什么 Dashboard 只是观察和干预面板，以及为什么不再额外造一层平行状态机。</p>
      </div>
    </div>
  </section>
</div>
