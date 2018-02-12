```
pragma solidity ^0.4.18;

// Import signal token
import './SignalToken.sol';

// Creating new contract
contract SignalTokenProtocol {
  // Define a new struct Campaign
  struct Campaign {
    address advertiser;
    string title;
    string description;
    string contentUrl;
    uint256 reward;
    uint256 budget;
  }

  //
  mapping(uint256 => Campaign) public campaigns;
  uint256[] public campaignsTable;

  SignalToken public signalToken;

  function SignalTokenProtocol() public {
    signalToken = new SignalToken(this);
  }

  function getCampaignsCount()
    public
    view
    returns (uint256)
  {
    return campaignsTable.length;
  }

  function getSignalTokenAddress()
    public
    constant
    returns (SignalToken)
  {
    return signalToken;
  }

  function faucet()
    public
    returns (bool)
  {
    return signalToken.mint(msg.sender, 500000);
  }

  function createCampaign(
    string title,
    string description,
    string contentUrl,
    uint256 reward,
    uint256 budget
  )
    public
    returns (uint256 campaignId)
  {
    campaignId = campaignsTable.length++;
    campaignsTable[campaignId] = campaignId;

    campaigns[campaignId] = Campaign(
      msg.sender,
      title,
      description,
      contentUrl,
      reward,
      budget
    );

    return campaignId;
  }

  function getCampaign(uint256 campaignId)
    public
    view
    returns (
      address advertiser,
      string title,
      string description,
      string contentUrl,
      uint256 reward,
      uint256 budget
    )
  {
    Campaign storage campaign = campaigns[campaignId];

    advertiser = campaign.advertiser;
    title = campaign.title;
    description = campaign.description;
    contentUrl = campaign.contentUrl;
    reward = campaign.reward;
    budget = campaign.budget;

    return (advertiser, title, description, contentUrl, reward, budget);
  }

  function executeCampaign(uint256 campaignId, address publisher)
    public
    returns (bool)
  {
    Campaign storage campaign = campaigns[campaignId];
    return executeTransfer(campaign.advertiser, publisher, campaign.reward);
  }

  function executeTransfer(
    address advertiser,
    address publisher,
    uint256 reward
  )
    private
    returns (bool)
  {
    return signalToken.transferFrom(advertiser, publisher, reward);
  }
}
```