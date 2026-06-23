# AMHS Mobile HMI Terminal - Technical Implementation Guide

This document details the software architecture, operational flows, and safety interlocks engineered into the AMHS Mobile HMI frontend terminal, tracking requirements F-001 through F-009 from the design specifications.

---

## 1. System Architecture

The application implements an **App Shell architecture** utilizing ASP.NET Core Razor Pages for view structures and an independent JavaScript Core layer (`amhs-core.js`) acting as the communication broker. This ensures local rendering stability and error masking when the mobile terminal enters factory Wi-Fi dead zones.

```mermaid
graph TD
    subgraph Client_Side [PWA Client Terminal (Mobile HMI)]
        A[Shared Layout Shell] -->|Tab Route| B[SCR-001: メイン操作盤]
        A -->|Tab Route| C[SCR-002: ストッカー一覧]
        A -->|Tab Route| D[SCR-003: 棚・在庫ステータス]
        A -->|Tab Route| E[SCR-004: ジョブ監視]
        A -->|Tab Route| F[SCR-005: 履歴ログ]
        
        B & C & D & E & F -->|Event Link| G[wwwroot/js/amhs-core.js <br> UI Rendering Engine]
    end

    subgraph Server_Side [Backend & Factory Layer]
        I[Real Server REST API <br> /api/* Endpoints]
        J[Physical Stocker Equipment]
        I <--> J
    end

    G -->|3000ms Polling Loop / FETCH / POST| I
    G -->|3000ms Polling Loop / FETCH / POST| I
