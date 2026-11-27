# EVE SSO Account Eligibility and Linked-Application Investigation

**Recorded:** 2026-11-26  
**Status:** Investigation note; suspected cause not yet confirmed  
**Scope:** EVE Dashboard multi-account authorization / CCP Developer Portal access

## Reported error

While adding/authorizing another EVE account in EVE Dashboard, the EVE SSO flow returned:

```json
{
  "error": "invalid_scope",
  "error_description": "The requested 'esi-mail.send_mail.v1' scope is not valid."
}
```

The error identifies the requested ESI scope `esi-mail.send_mail.v1`. It does not itself state that the account lacks qualifying transactions or developer eligibility. Keep the OAuth scope-registration question distinct from the account-eligibility investigation below.

## Account checks performed by the user

The user compared seven EVE accounts and checked the CCP Developer Portal eligibility indicators and account behavior:

| Account group | Valid transactions | Developer Agreement / application management | EVE Dashboard result |
|---|---|---|---|
| `neon_red` | Checked / present | Developer Agreement signed; can manage the registered application | Works |
| `neon_blue` | Checked / present | Developer Agreement not signed; no application-management option shown | Successfully added; works |
| Five newer accounts | Not checked / absent | Not able to proceed with linked-application setup | Not yet added |

All accounts use the same email address. The shared email therefore does not explain the difference observed in the valid-transactions indicator.

### Account and transaction history supplied by the user

- `neon_red` is an established account with subscription-payment history. The user believes those paid subscriptions explain its valid-transactions status.
- `neon_blue` is also an established/eligible account. The user found two character-transfer transactions associated with it, dated **2024-11-23** and **2026-01-17**. The later transfer may have been the transfer used to establish `neon_orange`; the user described that association as likely, not certain.
- The user expanded the fleet by creating additional accounts and transferring trained alternate characters into new accounts.
- The five newer accounts were funded with PLEX rather than subscription payments, and the user checked that none of the five shows valid transactions.
- Character ownership/transfer history and qualifying transaction history are account-level observations; do not assume a character transfer moves the originating account's transaction eligibility to the destination account.

## Current interpretation / hypothesis

**User's working hypothesis:** CCP requires qualifying paid transaction history on each EVE account before that account can be added/authorized through the Developer Portal's linked-application process. The five newer PLEX-funded accounts lack the valid-transactions indicator, while `neon_red` and `neon_blue` have it and both work in EVE Dashboard.

The account comparison makes this a plausible explanation for why the five accounts cannot currently be added. The user considers the missing valid-transactions indicator the key difference between `neon_blue` and the five newer accounts.

This remains a hypothesis, not a confirmed CCP rule. The precise transaction types that satisfy the check, and whether a new subscription payment or a particular EVE Store purchase qualifies, have not been verified.

## Proposed test and possible resolution

The user is **not ready to make a purchase or run this test yet**. When ready:

1. Choose one of the five accounts that currently lacks the valid-transactions indicator.
2. Make a transaction that CCP confirms is eligible (do not assume an arbitrary purchase or subscription qualifies).
3. Check whether the Developer Portal's valid-transactions indicator changes for that account.
4. Retry adding that account to the existing EVE Dashboard application.
5. If the account can then authorize successfully, record the exact transaction type and result before repeating anything on the remaining accounts.

Before spending money solely to unlock developer access, confirm with CCP Support or authoritative CCP documentation which transaction type qualifies. Do not purchase for all five accounts until a single-account test succeeds.

## Separate OAuth scope follow-up

The `invalid_scope` response must also be investigated independently:

- The dashboard application was registered and configured under `neon_red`.
- `neon_blue` was added to the dashboard using the existing application; it does not have its own application registration.
- Check the allowed/requested scopes on the application managed by `neon_red`, specifically whether `esi-mail.send_mail.v1` is enabled and whether the dashboard actually needs send-mail permission.
- If the application does not need to send EVE mail, remove the unnecessary scope from its authorization request. If it does need the capability, configure the scope in the developer application as permitted by CCP and reauthorize as required.
- Do not treat the `invalid_scope` message as proof of missing account transactions. The error text points to scope validity/configuration; account eligibility may be a separate gate encountered when trying to add the five other accounts.

## Evidence labels for future work

- **User-observed:** Exact SSO error; valid-transactions indicators on the checked accounts; `neon_red` and `neon_blue` working in the dashboard; `neon_blue` lacking the Developer Agreement signature and application-management option; five newer accounts lacking valid transactions.
- **User-reported account history:** Subscription payments on `neon_red`; two character-transfer transactions on `neon_blue` dated 2024-11-23 and 2026-01-17; newer accounts funded with PLEX; trained alts transferred to build the fleet.
- **Unverified hypothesis:** Qualifying transaction history is required per account for linked-application authorization/management, and obtaining a qualifying transaction will enable the five newer accounts.
- **Not yet tested:** Making a qualifying transaction on one of the five accounts and retrying the dashboard authorization.
