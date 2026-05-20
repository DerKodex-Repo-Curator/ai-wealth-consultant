# Banking

> **Status**: TEMPLATE — Bruno's beat. Populated by `/onboard`. Lists every account, its purpose, currency, and any open mortgage / credit-line.

## Account architecture

Replicate this block per institution:

### <Institution name>

- **Type**: <retail bank / private bank / building society / fintech / broker cash account>
- **Country**: <DE / UK / other>
- **Account holder**: <user / joint with spouse / corporate entity>
- **Account number / IBAN**: <last 4 digits — full detail can sit in `documents/` if needed>
- **Purpose**: <salary inflow / household spending / cash reserve / corporate working capital / etc.>
- **Currency**: <EUR / GBP / USD / multi>
- **Balance posture**: <typical balance range, not exact>
- **Notes**: <relationship manager name, fee structure, FSCS/Einlagensicherung coverage>

## Credit / mortgages

| Lender | Type | Outstanding | Rate | Fixed-rate end | Currency | Asset |
|---|---|---|---|---|---|---|
| <e.g. lender> | <mortgage / personal loan / overdraft> | <amount> | <rate> | <date> | <currency> | <property / general> |

## Credit profile

- **DE SCHUFA**: <score band, last checked date>
- **UK credit reference**: <Experian / Equifax / Cifas — score band, last checked>
- **Active credit limits**: <total available revolving credit>
- **Active disputes / adverse markers**: <none / detail>

## FX posture

- **Currency exposures**: <e.g. EUR salary, GBP rental income, USD broker base>
- **Hedging policy**: <none / partial / programmatic>
- **Open FX positions**: <if any forwards or swaps>

---

`verified: YYYY-MM-DD`
