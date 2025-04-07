Legal : 

- Is it considered discretionary trading ? → what are the legal implications of this ?
    - **Owner(s)** : Nathan, ???

Product : 

- What is gained (and given up) from unioning ?
    - What are the different types of strategies and what might be valuable about their differences ?
        - Isolating risk
            - Duration risk vs. no duration risk
            - Different types of counterparty risk ? (e.g., smart contract vs. binance vs. CME)
            - Different types of legal risk ? (e.g., a fully on-chain strategy vs. a CEX strategy vs. a CME strategy)
        - Fixed rates (easier for borrow arbitrage)
        - Withdrawal process is different strategies
            - A joint withdrawal process might be a major headache
    - What is our theory for how network effects works ? How can we (in)validate what we are actually getting from this ?
    - How do we want to segregate risk properly ?
- What does it do for complexity → what does complexity do for investors due diligence ?
- Lido v3 model thoughts
    - Commingling happens on the smart contract level → very transparent, non-custodial (less like a hedge fund)
    - Drastically increases smart contract complexity

Business development : 

- Off vs. On-chain integrations ?
    - Isolated strategies → off-chain integrations
    - Unioned strategy → on-chain integrations
- Integrations :
    - Getting individual (isolated) listings will probably make it more difficult
- How does this affect supply capacity frameworks and LTV parameters ?
    - When you change your strategy, will AAVE (or other lending protocols) need to update parameters ?
    - Does the prospect of changing the strategy affect it’s initial listing parameters (supply capacity and LTV) ?

Action items : 

- Chat with Nathan (Elijah and Mitya and J (if back from holiday))
    - Better understand what constitutes “discretionary” trading ?
- Schedule separate brainstorm (w Frank, Kai, Mitya, J) to discuss how “Lido v3”-esque architecture (i.e., individual strategies as an option, on-chain rebalancing for an “alloyed” token as the primary approach) might work. Questions to answer :
    - Is it practical ?
    - How might it work ?
    - Is it overly complex (engineering) for v1 ?
    - Is it overly complex to asses the risk ?
    - How path dependent is this ?
        - How easy / hard is it do after initial launch ?
- Credit market (and other integration) parameterization (Karen, Elijah, J)
    - How would changing the strategy affect the LTV and Supply cap parameters
    - Mars, Dolomite ???
    
    [Takara](https://www.notion.so/Takara-1b485d6b9b1080d5b82df7e9ca06708f?pvs=21)