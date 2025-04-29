# Registry Program Reference

## Overview

The Registry Program is a core component of the Machine-Centric Protocol (MCP) that manages the on-chain registry of agents, contexts, and permissions. This program serves as the "source of truth" for the protocol's identity and access control system.

## Program ID

```
Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS
```

## Account Structures

### Registry

The `Registry` account is the central registry that tracks all agents and contexts in the system.

```rust
#[account]
pub struct Registry {
    pub authority: Pubkey,
    pub agent_count: u64,
    pub context_count: u64,
}
```

| Field | Type | Description |
|-------|------|-------------|
| `authority` | `Pubkey` | The authority that controls the registry |
| `agent_count` | `u64` | The total number of registered agents |
| `context_count` | `u64` | The total number of registered contexts |

### Agent

The `Agent` account represents a registered agent in the system.

```rust
#[account]
pub struct Agent {
    pub authority: Pubkey,
    pub name: String,
    pub active: bool,
    pub created_at: i64,
}
```

| Field | Type | Description |
|-------|------|-------------|
| `authority` | `Pubkey` | The authority that controls the agent |
| `name` | `String` | The name of the agent |
| `active` | `bool` | Whether the agent is active |
| `created_at` | `i64` | Unix timestamp when the agent was created |

### Context

The `Context` account represents a registered context in the system.

```rust
#[account]
pub struct Context {
    pub authority: Pubkey,
    pub name: String,
    pub active: bool,
    pub created_at: i64,
}
```

| Field | Type | Description |
|-------|------|-------------|
| `authority` | `Pubkey` | The authority that controls the context |
| `name` | `String` | The name of the context |
| `active` | `bool` | Whether the context is active |
| `created_at` | `i64` | Unix timestamp when the context was created |

### Permission

The `Permission` account represents permission for an agent to access a context.

```rust
#[account]
pub struct Permission {
    pub agent: Pubkey,
    pub context: Pubkey,
    pub granted_by: Pubkey,
    pub granted_at: i64,
    pub active: bool,
}
```

| Field | Type | Description |
|-------|------|-------------|
| `agent` | `Pubkey` | The agent granted permission |
| `context` | `Pubkey` | The context the agent can access |
| `granted_by` | `Pubkey` | The authority that granted the permission |
| `granted_at` | `i64` | Unix timestamp when the permission was granted |
| `active` | `bool` | Whether the permission is active |

## Instructions

### Initialize

Initializes a new registry.

```rust
pub fn initialize(ctx: Context<Initialize>) -> Result<()>
```

#### Accounts

- `registry`: The registry account to initialize
- `authority`: The authority that will control the registry
- `system_program`: The system program

### Register Agent

Registers a new agent in the registry.

```rust
pub fn register_agent(ctx: Context<RegisterAgent>, name: String) -> Result<()>
```

#### Accounts

- `registry`: The registry account
- `agent`: The agent account to initialize
- `authority`: The authority that will control the agent
- `system_program`: The system program

#### Parameters

- `name`: The name of the agent

### Register Context

Registers a new context in the registry.

```rust
pub fn register_context(ctx: Context<RegisterContext>, name: String) -> Result<()>
```

#### Accounts

- `registry`: The registry account
- `context`: The context account to initialize
- `authority`: The authority that will control the context
- `system_program`: The system program

#### Parameters

- `name`: The name of the context

### Grant Permission

Grants permission for an agent to access a context.

```rust
pub fn grant_permission(ctx: Context<GrantPermission>) -> Result<()>
```

#### Accounts

- `agent`: The agent account
- `context`: The context account
- `permission`: The permission account to initialize
- `authority`: The authority granting the permission
- `system_program`: The system program

### Revoke Permission

Revokes permission for an agent to access a context.

```rust
pub fn revoke_permission(ctx: Context<RevokePermission>) -> Result<()>
```

#### Accounts

- `permission`: The permission account
- `authority`: The authority revoking the permission

## Client Usage

### Initializing a Registry

```typescript
import * as anchor from '@project-serum/anchor';
import { Program } from '@project-serum/anchor';
import { Registry } from '../target/types/registry';

async function initializeRegistry() {
  const program = anchor.workspace.Registry as Program<Registry>;
  
  const registry = anchor.web3.Keypair.generate();
  const authority = anchor.getProvider().wallet;
  
  await program.methods
    .initialize()
    .accounts({
      registry: registry.publicKey,
      authority: authority.publicKey,
      systemProgram: anchor.web3.SystemProgram.programId,
    })
    .signers([registry])
    .rpc();
    
  console.log('Registry initialized with address:', registry.publicKey.toString());
  return registry;
}
```

### Registering an Agent

```typescript
async function registerAgent(
  registryPublicKey: anchor.web3.PublicKey,
  agentName: string
) {
  const program = anchor.workspace.Registry as Program<Registry>;
  
  const agent = anchor.web3.Keypair.generate();
  const authority = anchor.getProvider().wallet;
  
  await program.methods
    .registerAgent(agentName)
    .accounts({
      registry: registryPublicKey,
      agent: agent.publicKey,
      authority: authority.publicKey,
      systemProgram: anchor.web3.SystemProgram.programId,
    })
    .signers([agent])
    .rpc();
    
  console.log('Agent registered with address:', agent.publicKey.toString());
  return agent;
}
```

### Granting Permission

```typescript
async function grantPermission(
  agentPublicKey: anchor.web3.PublicKey,
  contextPublicKey: anchor.web3.PublicKey
) {
  const program = anchor.workspace.Registry as Program<Registry>;
  
  const permission = anchor.web3.Keypair.generate();
  const authority = anchor.getProvider().wallet;
  
  await program.methods
    .grantPermission()
    .accounts({
      agent: agentPublicKey,
      context: contextPublicKey,
      permission: permission.publicKey,
      authority: authority.publicKey,
      systemProgram: anchor.web3.SystemProgram.programId,
    })
    .signers([permission])
    .rpc();
    
  console.log('Permission granted with address:', permission.publicKey.toString());
  return permission;
}
```

## Security Considerations

- The registry program enforces ownership checks to ensure that only the appropriate authorities can manage agents, contexts, and permissions.
- When integrating with the registry program, ensure that the correct authorities are used for signing transactions.
- All operations that modify the state of the registry require proper authorization.

## Related Resources

- [Execution Program](./execution.md)
- [Agent API](../api/agent-api.md)
- [Context Integration Guide](../guides/context-integration.md)
