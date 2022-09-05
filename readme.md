# Solidity-Styling-Guide-Diamonds
This is the solidity styling guide used by Sibling Labs for smart contracts which are part of a project which adheres to EIP-2535 Diamonds.

## Introduction

This style guide is designed to be used in conjunction with the original [Sibling Labs solidity style guide](https://github.com/NFTSiblings/Siblings-Solidity-Styling-Guide), and relates directly to smart contracts which are a part of a project which adheres to EIP-2535 Diamonds. Covered in this document are facet contracts, base diamond contracts and diamond initialiser contracts.

This guide's intended purpose is to provide the convention for writing solidity code. This guide is an evolving document and will incorporate future advancements in the language, improvements to the readability of contracts, and phase out outdated conventions.

## Cover Paragraph

A cover paragraph is a multi-line comment which makes key details about the smart contract quickly available.

The cover paragraph should be near the top of the file, below the SPDX license identifier and pragma statement, but above imports.

Cover paragraphs in files which contain an original facet contract or relevant solidity library authored by Sibling Labs should follow this format:
```
/**************************************************************\
 * RoyaltiesConfigFacet authored by Sibling Labs
 * Version 0.1.0
/**************************************************************/
```

Additional notes may be provided in the cover paragraph. For example:
```
/**************************************************************\
 * AdminPrivilegesFacet authored by Sibling Labs
 * Version 0.1.0
 * 
 * Adheres to ERC-173
/**************************************************************/
```

Facet contracts or relevant solidity libraries which are adapted from a reference implementation of EIP-2535 or other work from Nick Mudge, creator of the diamond standard, should use the following format:
```
/**************************************************************\
 * Diamond contract authored by Sibling Labs
 * Version 0.1.0
 * 
 * Adapted from work by Nick Mudge
 * <nick@perfectabstractions.com> (https://twitter.com/mudgen)
 * 
 * This contract is part of a project which adheres to
 * EIP-2535 Diamonds: https://eips.ethereum.org/EIPS/eip-2535
/**************************************************************/
```

For files which contain a solidity library related to a facet contract, an additional note should be included at the bottom of the cover paragraph:
```
 * This library is designed to work in conjunction with
 * AllowlistFacet - it facilitates diamond storage and shared
 * functionality associated with AllowlistFacet.
 ```

For diamond initialiser contracts, an additional note should be included at the bottom of the cover paragraph:
```
 * This initialiser contract has been written specifically for
 * ERC721A-DIAMOND-TEMPLATE by Sibling Labs
```

Note that the cover paragraph adheres to the per line character limit (65 characters) for comments set out in the original Sibling Labs style guide.

## Diamond Contracts

Diamond contracts, meaning the base diamond contract of a given project, should be copied from one of our team's templates. Most diamond projects won't require modifications to the diamond contract, but modifications are allowed if necessary.

If changes are necessary to the diamond contract, they should be indicated clearly with explanatory comments.

## Facet Contracts

Versioning is key to maintaining facets and diamond contracts. Make sure to update the version number in the cover paragraph of the file after making any changes!

The contents of a facet contract should be structured as follows:
1. Events
2. Variable getter functions
3. Remaining functions

Other than distinguishing the variable getters from the rest of the functions in a facet contract, the functions do not need to be categorised. Functions should be ordered by their visibility though, just as in standard Sibling Labs smart contracts.

NATSPEC comments must be added for every function in a facet contract. If a function is required by a particular standard, it should be indicated in the NATSPEC comment. Example:
```
/**
 * @dev Returns address of contract owner. Required by
 *      ERC-173.
 */
 ```

## Facet Libraries

Most facet contracts will have an associated facet library. Facet libraries are used for shared functionality, and [diamond storage](https://dev.to/mudgen/how-diamond-storage-works-90e). Shared functionality refers to any functions which need to be shared between facets.

All functions in a facet library should be internal.

All facet libraries should include functionality related to diamond storage at the top of the library, including:
    - A private bytes32 variable called `DIAMOND_STORAGE_POSITION`
    - A struct called `state` with all state variables
    - The following getter function:
    ```
    /**
    * @dev Return stored state struct.
    */
    function getState() internal pure returns (state storage _state) {
        bytes32 position = DIAMOND_STORAGE_POSITION;
        assembly {
            _state.slot := position
        }
    }
    ```

After the above code, other functions may be included.

Libraries cannot have modifiers, but functions can be used in a similar way, for example:
```
    /**
    * @dev Reverts if the private sale is not active. Use this
    *      function as needed in other facets to ensure that
    *      particular functions may only be called during the
    *      private sale.
    */
    function requirePrivSaleIsActive() internal view {
        require(isPrivSaleActive(), "PrivSalePeriodFacet: private sale is not active now");
    }
```

Shared functions in a facet library can be imported into other facets to share functionality between facets of a diamond. Don't forget to add the shared function in the library into the library's facet contract too - functions from the library are not automatically included in the facet contract.

Variable getter and setter functions are not necessary for facet libraries.

## Diamond Initialiser Contract

Diamond initialiser contracts are unique to each diamond. They are designed to be used only once.

Diamond initialiser contracts may have state variables which do not conform to the diamond storage pattern.

Initialiser functions should be separate for each facet contract. For example, a diamond initialiser contract may include one intialiser function for its allowlist facet, one initialiser function for its AdminPrivilegesFacet, and so on.

Functions should follow this naming convention: `initFacetName`. For example: `initAllowlistFacet`.

Functions should not have arguments.

Functions should have NATSPEC comments which make it clear which facet the function is designed to initialise, and should include a link to the verified facet contract on Etherscan.