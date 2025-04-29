# Agent API Reference

## Overview

The MCP Agent API provides a standardized interface for building and integrating AI agents with the Machine-Centric Protocol. This document outlines the core interfaces, methods, and patterns for agent development.

## Core Interfaces

### Agent

The base `Agent` interface defines the fundamental capabilities that all MCP agents must implement:

```typescript
interface Agent {
  // Basic information
  getName(): string;
  getDescription(): string;
  getCapabilities(): string[];
  
  // Core functionality
  processInstruction(instruction: string): Promise<AgentResponse>;
  executeTransaction(transaction: Transaction): Promise<string>;
  getState(): Promise<Record<string, any>>;
}
```

### AgentContext

The `AgentContext` interface provides the initialization context for an agent:

```typescript
interface AgentContext {
  agentId: PublicKey;
  connection: Connection;
  contextId?: PublicKey;
}
```

### AgentResponse

The `AgentResponse` interface defines the standardized response format for agent operations:

```typescript
interface AgentResponse {
  success: boolean;
  message: string;
  data?: any;
  transactionId?: string;
  error?: {
    code: number;
    message: string;
  };
}
```

## Agent Lifecycle

1. **Initialization**: Agents are initialized with an `AgentContext` containing the agent's on-chain identity and connection information.

2. **Registration**: Agents must be registered in the MCP Registry to be discoverable and accessible within the protocol.

3. **Instruction Processing**: Agents receive natural language or structured instructions through the `processInstruction` method.

4. **Transaction Execution**: When an instruction requires on-chain interaction, agents construct and submit transactions using the `executeTransaction` method.

5. **State Management**: Agents maintain their state and can report it through the `getState` method.

## Implementation Example

Below is a simplified example of a basic agent implementation:

```typescript
import { Connection, PublicKey, Transaction } from '@solana/web3.js';
import { Agent, AgentContext, AgentResponse } from 'mcp-agents';

class SimpleAgent implements Agent {
  private agentId: PublicKey;
  private connection: Connection;
  
  constructor(context: AgentContext) {
    this.agentId = context.agentId;
    this.connection = context.connection;
  }
  
  getName(): string {
    return 'Simple Agent';
  }
  
  getDescription(): string {
    return 'A basic example agent for demonstration purposes';
  }
  
  getCapabilities(): string[] {
    return ['basic_operations'];
  }
  
  async processInstruction(instruction: string): Promise<AgentResponse> {
    // Parse and process the instruction
    return {
      success: true,
      message: `Processed: ${instruction}`,
      data: {
        timestamp: new Date().toISOString()
      }
    };
  }
  
  async executeTransaction(transaction: Transaction): Promise<string> {
    // Sign and submit the transaction
    return 'simulated-transaction-signature';
  }
  
  async getState(): Promise<Record<string, any>> {
    return {
      agentId: this.agentId.toString(),
      timestamp: new Date().toISOString()
    };
  }
}
```

## Specialized Agent Types

MCP provides several specialized agent base classes that extend the core `Agent` interface with domain-specific functionality:

### DeFi Agents

DeFi agents provide specialized capabilities for interacting with decentralized finance protocols:

```typescript
interface DeFiAgentContext extends AgentContext {
  supportedDexes: string[];
  defaultSlippageBps: number;
}

class DeFiAgent extends Agent {
  // DeFi-specific methods
  async swapTokens(params: SwapParams): Promise<AgentResponse>;
  async provideLiquidity(params: LiquidityParams): Promise<AgentResponse>;
  async checkPrice(token: string): Promise<AgentResponse>;
}
```

### NFT Agents

NFT agents specialize in non-fungible token operations:

```typescript
interface NFTAgentContext extends AgentContext {
  supportedMarketplaces: string[];
  defaultRoyaltyBps: number;
}

class NFTAgent extends Agent {
  // NFT-specific methods
  async listNFT(params: ListingParams): Promise<AgentResponse>;
  async buyNFT(params: BuyParams): Promise<AgentResponse>;
  async mintNFT(params: MintParams): Promise<AgentResponse>;
}
```

## Best Practices

When implementing agents for the MCP ecosystem, follow these best practices:

1. **Security**: Never store private keys within the agent code. Use wallet adapters or other secure signing mechanisms.

2. **Error Handling**: Provide clear error messages and codes in the `AgentResponse` to help diagnose issues.

3. **Instruction Parsing**: Use robust NLP techniques to parse natural language instructions accurately.

4. **Transaction Simulation**: Simulate transactions before execution to catch potential errors.

5. **Context Awareness**: Agents should be aware of their permissions and only interact with authorized contexts.

6. **State Management**: Maintain minimal state in the agent and rely on on-chain data when possible.

## Further Reading

- [Agent Development Guide](../guides/agent-development.md)
- [Context Integration](../guides/context-integration.md)
- [Registry Program](../programs/registry.md)
- [Execution Program](../programs/execution.md)
