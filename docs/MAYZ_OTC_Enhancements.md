# MAYZ Trustless OTC Smart Contract - Proposed Enhancements

## Executive Summary
This document outlines proposed enhancements to the MAYZ Trustless OTC Smart Contract aimed at increasing flexibility, improving market efficiency, and providing better price protection mechanisms. These enhancements build upon the existing architecture while introducing new features to better serve various trading scenarios.

## 1. Flexible Token Issuance

### Current Limitation
The current implementation only allows minting a single NFT representing the entire token deposit, which may limit trading flexibility for large positions.

### Proposed Enhancement
Introduce support for minting multiple Fungible Tokens (FTs) instead of a single NFT, allowing position creators to fragment their large positions into smaller, more tradeable units.

#### Implementation Details
- **Smart Contract Changes**:
CODE
type OTCDatum {
    /// Existing fields
    ft_total_supply: Int,
    ft_tokens_per_unit: Int,
    is_fractional: Bool
}
CODE

- **Validation Rules**:
  - Total underlying tokens must equal `ft_total_supply * ft_tokens_per_unit`
  - Minimum size per FT unit to prevent dust
  - Maximum FT supply limit for efficiency

#### Benefits
- Improved liquidity through position fragmentation
- More accessible trading for smaller buyers
- Flexible exit strategies for large position holders

## 2. In-Contract Exchange Mechanism

### Current Limitation
OTC tokens must be traded on external marketplaces, adding complexity and potential friction to the trading process.

### Proposed Enhancement
Enable direct token exchanges within the OTC contract using either fixed prices or oracle-based pricing.

#### Implementation Details
CODE
type OTCDatum {
  /// Existing fields
  price_in_ada: Int,
  price_valid_until: POSIXTime,
  use_oracle: Bool,
  oracle_config: Option<OracleConfig>,
  /// Track the number of sales for position analytics
  total_sales: Int,  
  /// Track total ADA volume for position analytics
  total_volume: Int  
}

type OracleConfig {
  oracle_policy_id: PolicyId,
  price_tolerance: Int,
  update_frequency: POSIXTime
}
CODE

#### Features
1. **Fixed Price Trading**
   - Creator sets ADA price per token
   - Direct purchase using contract endpoints
   - MAYZ token return on sale completed
   - Sales and volume tracking

2. **Oracle Integration** (Future Phase)
   - Price feeds from trusted oracles
   - Configurable price tolerance bands
   - Automatic price updates

## 3. Price Protection Mechanisms

### Proposed Enhancement
Implement time-based price validity and protection mechanisms to manage price volatility risks.

#### Features
- Automatic order suspension when price validity expires
- Configurable price bounds for oracle-based trading
- Grace period for price updates before order cancellation

## 4. Offers Contract

### Proposed Enhancement
Implement a dedicated contract for managing buy offers with locked ADA collateral.

#### Implementation Details

TODO: quisiera que pueda crear oferta por una x cantidad de tokens OTC a un precio x por cada token
TODO: agregar posibilidad de que no sea una venta por el total, si no tambien algo parcial 

CODE
type OfferDatum {
  otc_token_policy_id: PolicyId,
  otc_token_name: TokenName,
  offer_amount: Int,
  valid_until: POSIXTime,
  buyer_address: Address,
  offer_status: OfferStatus
}

type OfferStatus {
  Active | Expired | Canceled | Accepted
}
CODE

#### Key Features
1. **Offer Creation**
   - Lock ADA with offer price
   - Set validity period
   - Specify target OTC tokens

2. **Offer Management**
   - Update offer amount
   - Extend validity period
   - Cancel and reclaim ADA

3. **Offer Acceptance**
   - Swap of tokens and ADA

## 5. MAYZ Token Management

### Current Limitation
MAYZ tokens remain locked until full position closure, limiting capital efficiency.

### Proposed Enhancement
Enable MAYZ token recovery when position is closed through direct exchange. If ADA from a direct sale is present in the contract, the creator can recover their MAYZ tokens without requiring the OTC NFT, since the presence of sale proceeds indicates successful position closure.

TODO: explicar que este es un beneficio, por sobre la alternativa de que el creador reciva los nFT y los ponga a la venta en jpg store, por que ese caso, un comprador puede comprar el nft, llevarselo, pero nunca reclamar los tokens en la posicion, y entonces el creador no puede reclamar sus mayz. es una especie de bloqueo que no podemos evitar en ese camino.

## 6. Order and Offer Updates

### Proposed Enhancement
Implement comprehensive update mechanisms for both OTC orders and offers.

#### Features
1. **Order Updates**
   - Price adjustments
   - Validity period extensions
   - Partial cancellations for fractional positions

2. **Offer Updates**
   - Price modifications
   - Validity extensions
   - Partial offer adjustments
  





****




-------------------------










# MAYZ Trustless OTC Smart Contract - Proposed Enhancements

## Executive Summary
This document outlines proposed enhancements to the MAYZ Trustless OTC Smart Contract aimed at increasing flexibility, improving market efficiency, and providing better price protection mechanisms. These enhancements build upon the existing architecture while introducing new features to better serve various trading scenarios.

## 1. Flexible Token Issuance

### Current Limitation
The current implementation only allows minting a single NFT representing the entire token deposit, which may limit trading flexibility for large positions.

### Proposed Enhancement
Introduce support for minting multiple Fungible Tokens (FTs) instead of a single NFT, allowing position creators to fragment their large positions into smaller, more tradeable units.

#### Implementation Details
- **Smart Contract Changes**:
CODE
type OTCDatum {
    /// Existing fields
    ft_total_supply: Int,
    ft_tokens_per_unit: Int,
    is_fractional: Bool
}
CODE

- **Validation Rules**:
  - Total underlying tokens must equal `ft_total_supply * ft_tokens_per_unit`
  - Minimum size per FT unit to prevent dust
  - Maximum FT supply limit for efficiency

#### Benefits
- Improved liquidity through position fragmentation
- More accessible trading for smaller buyers
- Flexible exit strategies for large position holders

## 2. In-Contract Exchange Mechanism

### Current Limitation
OTC tokens must be traded on external marketplaces, adding complexity and potential friction to the trading process.

### Proposed Enhancement
Enable direct token exchanges within the OTC contract using either fixed prices or oracle-based pricing.

#### Implementation Details
CODE
type OTCDatum {
  /// Existing fields
  price_in_ada: Int,
  price_valid_until: POSIXTime,
  use_oracle: Bool,
  oracle_config: Option<OracleConfig>,
  /// Track the number of sales for position analytics
  total_sales: Int,  
  /// Track total ADA volume for position analytics
  total_volume: Int  
}

type OracleConfig {
  oracle_policy_id: PolicyId,
  price_tolerance: Int,
  update_frequency: POSIXTime
}
CODE

#### Features
1. **Fixed Price Trading**
   - Creator sets ADA price per token
   - Direct purchase using contract endpoints
   - MAYZ token return on sale completed
   - Sales and volume tracking

2. **Oracle Integration** (Future Phase)
   - Price feeds from trusted oracles
   - Configurable price tolerance bands
   - Automatic price updates

## 3. Price Protection Mechanisms

### Proposed Enhancement
Implement time-based price validity and protection mechanisms to manage price volatility risks.

#### Features
- Automatic order suspension when price validity expires
- Configurable price bounds for oracle-based trading
- Grace period for price updates before order cancellation

## 4. Offers Contract

### Proposed Enhancement
Implement a dedicated contract for managing buy offers with locked ADA collateral.

TODO: registro de parcialidad ejecutada

#### Implementation Details
CODE
type OfferDatum {
  otc_token_policy_id: PolicyId,
  otc_token_name: TokenName,
  /// Amount of OTC tokens requested
  tokens_requested: Int,      
  /// Price willing to pay per token  
  price_per_token: Int,   
  /// Total ADA amount locked (tokens_requested * price_per_token)     
  total_offer_amount: Int,     
  valid_until: POSIXTime,
  buyer_address: Address,
  offer_status: OfferStatus,
  /// Whether partial fills are accepted
  allow_partial: Bool         
}

type OfferStatus {
  Active | Expired | Canceled | Accepted | Partial Accepted
}
CODE

#### Key Features
1. **Offer Creation**
   - Lock ADA with offer price per token
   - Set desired quantity of tokens
   - Enable partial fills if desired
   - Set validity period
   - Specify target OTC tokens

2. **Offer Management**
   - Update offer amount and price
   - Extend validity period
   - Cancel and reclaim ADA

3. **Offer Acceptance**
   - Full or partial token

## 5. MAYZ Token Management

### Current Limitation
MAYZ tokens remain locked until full position closure, limiting capital efficiency.

### Proposed Enhancement
Enable MAYZ token recovery when position is closed through direct exchange. If ADA from a direct sale is present in the contract, the creator can recover their MAYZ tokens without requiring the OTC NFT, since the presence of sale proceeds indicates successful position closure.

### Benefits of Direct Exchange vs External Marketplaces
The direct exchange mechanism provides a crucial advantage over traditional marketplace sales (like JPG Store). In the traditional approach, when a creator lists their OTC NFT on an external marketplace:

1. The NFT can be purchased and moved from the marketplace
2. The buyer may choose to never claim the underlying tokens from the OTC contract
3. As a result, the creator's MAYZ tokens remain permanently locked in the contract
4. There is no mechanism to verify if an external sale occurred or force token claiming

This creates a potential deadlock scenario where neither party can access the locked MAYZ tokens. 

By implementing direct exchange within the contract:

- Sales and token claims happen atomically
- MAYZ tokens can be released upon successful sale
- No risk of permanent MAYZ token locking
- Clear verification of sale completion through ADA presence

## 6. Order and Offer Updates

### Proposed Enhancement
Implement comprehensive update mechanisms for both OTC orders and offers.

#### Features
1. **Order Updates**
   - Price adjustments
   - Validity period extensions
   - Partial cancellations for fractional positions

2. **Offer Updates**
   - Price modifications
   - Validity extensions
   - Partial offer adjustments