# HIP-XXXX: Helium Mapper LNS

- Author(s): @gradoj
- Start Date: 2024-10-13
- Category: mapper, economic
- Original HIP PR: <!-- leave this empty; maintainer will fill in ID of this pull request -->
- Tracking Issue: <!-- leave this empty; maintainer will create a discussion issue -->
- Vote Requirements: veIOT Holders

## Summary

The **Helium Mapper LNS** proposes the creation of a dedicated LoRaWAN Network Server (LNS) tailored specifically for mappers within the Helium network. This system will streamline mapper onboarding and improve data quality to provide an accurate coverage map for Helium gateways. Similar to the Educational LNS, the Mapper LNS will offer flexible Data Credit (DC) pricing to encourage usage, while integrating seamlessly with the existing Helium ecosystem.

## Motivation

The primary goal of this proposal is to provide a dedicated infrastructure for Helium-based mappers, which would be used to map network coverage and help validate the performance of Helium hotspots. The dedicated Mapper LNS will address the following challenges:

- **Encouraging Mapper Usage**: Creating a dedicated Mapper LNS allows for flexible DC pricing or creative usage without the worry of DC gaming or farming. This will enable supplementing mapper DC usage from the IoT operation fund.
- **Simplifying Mapper Device Onboarding**: The Mapper LNS will remove any friction in onboarding, funding, and using the sensors by allowing trusted manufacturers to onboard the mapper sensors before they are sold.
- **Improving Mapper Data Quality**: By exclusively onboarding standardized, approved sensors from known manufacturers, the Mapper LNS will ensure the accuracy and reliability of the data used to evaluate network coverage.
- **Increased Sensor and DC Usage**: While providing a valuable service of mapping and verifying hotspot coverage, this system will increase DC usage.

## Stakeholders

**Mapper users**: Current mappers will require new sensors which have been securely onboarded as per the described process

**Mapper LNS Operator**: A mapper LNS provider(s) will be need to be selected. 

**Mapper Data Consumers**: Mapper data will need to be processed and archived in S3 buckets similar to POC data which is publicly available.

**Hotspot Operators**: Hotspot owners coverage map will be more available. Future PoC could be, in part, determined by this mapper data. For example, if a hotspot has not transmitted mapper data in the last month its PoC data could be disabled until mapper data has been seen. 


## Detailed Explanation
The primary objective of the Mapper LNS is the desire for a secure, controlled environment for trusted IoT sensors to verify hotspot validity and coverage. The Mapper LNS is modelled off of the Helium Foundation Education LNS and will be run on its on NetID. This architecture will allow a small number of trusted sensor manufacturers such as RAKWireless or SEEED to directly onboard sensors to be used as mappers. 

Mapper are typical LoRaWAN IoT sensors that will sent packets in an agreed upon data format and reward hotspots in exchange for packet transfer. Unlike the Educational LNS, which serves free dc, this proposal does not require the Helium Packet Router HPR to be configured in any special way to accomplish this setup. The only difference from a regular sensor is these mappers will contain keys that are not available to the end-user of the device. The sensor manufacturer has commissioned the keys in a secure manner on the sensor as well as the LNS enabling the sensor to map right out of the box. This does not guarantee the keys are won't be extracted directly from the sensor through advanced techniques. Sensors that store their keys in a secure enclave or similar are widely available today.

Each sensor in the mapper LNS will be supplemented from the Helium IoT Operation Fund up to 100,000 dc per month. The multi-buy option will be set to unlimited distributing the credits to all hotspots which report the data packets. The amount given to each sensor needs to be high enough to allow frequent packets to be sent with multi-buy enabled yet low enough that it doesn't make financial sense to purchase mapper hardware and farm dc through your own hotspot. At 100k dc per month it is unlikely that most mappers will require this much dc.

| Update Rate<br>(minutes) | Packets Sent<br>per Month | Reporting Hotspots per Packet<br>(to Consume 100k DC per Month) |
|--------------------------|---------------------------|-----------------------------------------------------------------|
| 0.5(30s)                 | 86,400                    | 1.2                                                             |
| 1                        | 43,200                    | 2.3                                                             |
| 10                       | 4,320                     | 23.1                                                            |
| 30                       | 1,440                     | 69.4                                                            |
| 60(1h)                   | 720                       | 138.8                                                           |

For example a mapper that sends a packet every 10 minutes for a month sends 4320 packets. It would take 23.1 hotspots reporting every packet to consume the 100k dc per month.

## Drawbacks

It will cost money to run the mapper LNS as well as processing and archiving the data. This data collection will give us a real-world coverage map that will show-case the footprint and capability of the network.

Another drawback to this architecture is a more central system with one LNS for mappers. Today mappers contribute from any LNS using any hardware.

Mapper data is sensitive geographic data that will need to be handled and anonymized in an approved method.

## Rationale and Alternatives

**Do nothing** Adhoc collection of random sensors has made network coverage mapping difficult. Today, mappers pay dc for their effort ensure only a small number of hobby users. This proposed system will allow us to safely pay mappers dc usage without the fear of dc farming.

**Pay for third party data** Excellent data sources such as DIMO exist that could be purchased. This data has already been curated and is currently available. The mapper LNS does not exclude any of these data sources. If appropriate, these sensors could even join the mapper LNS exchanging their metadata for data transfer.  


## Unresolved Questions

Is a NetID required?
We need to select a sensor and mfg and agree on standard data-packet format.
Who will run the LNS and what will it cost?
Who will develop the data pipeline and S3 buckets?

## Deployment Impact

Current mappers will not be able to participate in this mapper LNS and will need to purchase new hardware. This is unfortunate, however, most current mappers are hobby users who are not opposed to having various sensors and will most likely be willing to purchase another sensor for mapping.

The existing mapper endpoint could remain active as it consumes data from any LNS, however, it is all but unmaintained at this point with the data being inaccessible to the community.


## Success Metrics

Higher quality coverage data will be generated by more standardized mapper hardware.

An increase in sensor mapping hardware on the network. Off-the-shelf mapping sensor with dc paid for will help additional mappers come online.
