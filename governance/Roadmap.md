# Roadmap de Documentacion APIBO

## Objetivo

Este archivo sirve para llevar control de los endpoints ya documentados y de los que todavia quedan pendientes.

## Fuente de control

- Fuente principal del inventario: `EnpointsRoles.txt`
- Total de endpoints funcionales unicos relevados: `79`
- Regla de seguimiento: cuando un endpoint tenga mas de un verbo relevante, conviene marcarlo por verbo en este roadmap

## Estado actual

- Endpoints unicos ya documentados: `79`
- Endpoints unicos pendientes: `0`

## Ya documentados

| Endpoint | Verbo / alcance documentado | Archivo |
|---|---|---|
| `AccountBalance` | `GET` | `AccountBalance.md` |
| `AccountDefinition` | `GET` | `AccountDefinition.md` |
| `AccountDetails` | `GET` | `AccountDetails.md` |
| `AccountList` | `GET` | `AccountList.md` |
| `AccountRegistration` | `GET` | `AccountRegistration-GET.md` |
| `AccountRegistration` | `POST` | `AccountRegistration-POST.md` |
| `AccountStatus` | `POST` | `AccountStatus.md` |
| `AccountSummaryReport` | `GET` | `AccountSummaryReport.md` |
| `AccountUpdate` | `POST` | `AccountUpdate.md` |
| `AccruedFees` | `GET` | `AccruedFees.md` |
| `ActiveOrders` | `GET` | `ActiveOrders.md` |
| `AllocationInstruction` | `GET` | `AllocationInstruction.md` |
| `AllocationReport` | `GET` | `AllocationReport.md` |
| `BalanceTransfer` | `GET` | `BalanceTransfer-GET.md` |
| `BalanceTransfer` | `POST` | `BalanceTransfer-POST.md` |
| `CancelOrder` | `POST` | `CancelOrder.md` |
| `ChangePassword` | `POST` | `ChangePassword.md` |
| `ClearingMembers` | `GET` | `ClearingMembers.md` |
| `CollateralList` | `GET` | `Instrucciones-Garantia-CollateralList.md` |
| `CollateralAssignment` | `GET` | `CollateralAssignment-GET.md` |
| `CollateralAssignment` | `POST` | `CollateralAssignment-POST.md` |
| `ContractScenarios` | `GET` | `ContractScenarios.md` |
| `ContratosActivos` | `GET` | `ContratosActivos.md` |
| `CorporateActionCredits` | `GET` | `CorporateActionCredits.md` |
| `CreateNewUser` | `POST` | `CreateNewUser.md` |
| `CurrencyList` | `GET` | `CurrencyList.md` |
| `CustodyGlobalStock` | `GET` | `CustodyGlobalStock-GET.md` |
| `CustodyGlobalStock` | `POST` | `CustodyGlobalStock-POST.md` |
| `CustodyRegistration` | `POST` | `CustodyRegistration.md` |
| `DeliveryMarginRequirementReport` | `GET` | `DeliveryMarginRequirementReport.md` |
| `DeliveryQuotaRegistration` | `GET` | `DeliveryQuotaRegistration-GET.md` |
| `DeliveryQuotaRegistration` | `POST` | `DeliveryQuotaRegistration-POST.md` |
| `DepositaryAccountList` | `GET` | `DepositaryAccountList.md` |
| `DerivativeSecurityList` | `GET` | `DerivativeSecurityList.md` |
| `DigitalInstrument` | `GET` | `DigitalInstrument-GET.md` |
| `DigitalInstrument` | `POST` | `DigitalInstrument-POST.md` |
| `DigitalWalletDetails` | `GET` | `DigitalWalletDetails.md` |
| `DigitalWalletStock` | `GET` | `DigitalWalletStock.md` |
| `DigitalWalletSummary` | `GET` | `DigitalWalletSummary.md` |
| `ExecutionReport` | `GET` | `ExecutionReport.md` |
| `Fee` | `GET` | `Instrucciones-Garantia-Fee.md` |
| `FilledOrders` | `GET` | `FilledOrders.md` |
| `HistoryRegisters` | `GET` | `HistoryRegisters.md` |
| `HistoryRegistersHeader` | `GET` | `HistoryRegistersHeader.md` |
| `InstrumentParams` | `GET` | `InstrumentParams.md` |
| `Invoices` | `GET` | `Invoices.md` |
| `MarginRequirementReport` | `GET` | `Instrucciones-Garantia-MarginRequirementReport.md` |
| `MarginBalance` | `GET` | `MarginBalance.md` |
| `MT506` | `GET` | `Garantias-MT506.md` |
| `MT506ByAccountRegistry` | `GET` | `MT506ByAccountRegistry.md` |
| `MT536` | `GET` | `Movimientos-Activos-MT536.md` |
| `MT536ByAccountRegistry` | `GET` | `MT536ByAccountRegistry.md` |
| `MT940` | `GET` | `MT940.md` |
| `MarketData` | `GET` | `MarketData.md` |
| `NewCollateralReport` | `GET` | `Instrucciones-Garantia-NewCollateralReport-GET.md` |
| `NewCollateralReport` | `POST` | `Instrucciones-Garantia-NewCollateralReport-POST.md` |
| `NewOrderList` | `POST` | `NewOrderList.md` |
| `NewOrderSingle` | `POST` | `NewOrderSingle.md` |
| `OrderByID` | `GET` | `OrderByID.md` |
| `PartyDetails` | `GET` | `PartyDetails.md` |
| `PositionMaintenance` | `GET` | `PositionMaintenance.md` |
| `PositionMaintenanceByAccountRegistry` | `GET` | `PositionMaintenanceByAccountRegistry.md` |
| `PositionMaintenanceByAccountRegistryHeader` | `GET` | `PositionMaintenanceByAccountRegistryHeader.md` |
| `PositionReport` | `GET` | `PositionReport.md` |
| `PositionReportByAccountRegistry` | `GET` | `PositionReportByAccountRegistry.md` |
| `ProductsSpreadMargins` | `GET` | `ProductsSpreadMargins.md` |
| `RegistrationInstructions` | `GET` | `RegistrationInstructions-GET.md` |
| `RegistrationInstructions` | `POST` | `RegistrationInstructions-POST.md` |
| `RelatedAccount` | `GET` | `RelatedAccount.md` |
| `ReplaceOrder` | `POST` | `ReplaceOrder.md` |
| `ResetPassword` | `POST` | `ResetPassword.md` |
| `RiskPositionReport` | `GET` | `RiskPositionReport.md` |
| `SecurityDefinition` | `GET` | `SecurityDefinition.md` |
| `SecurityList` | `GET` | `SecurityList.md` |
| `SettlementStatusReport` | `GET` | `SettlementStatusReport.md` |
| `ShareholderPosition` | `GET` | `ShareholderPosition.md` |
| `ShareHolderPositionSG` | `POST` | `ShareHolderPositionSG.md` |
| `SpreadMargins` | `GET` | `SpreadMargins.md` |
| `ClosingProcesses` | `GET` | `ClosingProcesses.md` |
| `TradeAllocationRequest` | `POST` | `TradeAllocationRequest.md` |
| `TradeCaptureReport` | `GET` | `TradeCaptureReport-GET.md` |
| `TradeCaptureReport` | `POST` | `TradeCaptureReport-POST.md` |
| `TradeCaptureReportByAccountRegistry` | `GET` | `TradeCaptureReportByAccountRegistry.md` |
| `TradeCaptureReportFCI` | `GET` | `TradeCaptureReportFCI.md` |
| `TradeCaptureReportList` | `POST` | `TradeCaptureReportList.md` |
| `TradeMatchReport` | `POST` | `TradeMatchReport.md` |
| `contratos` | `Referencia legacy` | `contratos.md` |
| `facturas` | `Referencia legacy` | `facturas.md` |

## Pendientes

No quedan endpoints pendientes en el inventario actual.

## Notas de seguimiento

- `NewCollateralReport` ya esta cubierto por separado para `GET` y `POST`.
- Los endpoints multi verbo documentados se estan manteniendo en archivos separados por verbo, por ejemplo `...-GET.md` y `...-POST.md`.
- `contratos` y `facturas` quedaron cerrados como referencias legacy para no perder trazabilidad con inventarios historicos, pero no deben confundirse con rutas activas de APIBO.
- `TradeCaptureReportByAccountRegistry` quedo documentado como endpoint historico / inactivo, porque el controller aparece comentado en el repo actual aunque su BL siga existiendo.
- Si aparece documentacion vieja en `.doc` para alguno de estos endpoints, se debe conservar la estructura original y actualizar el contenido con codigo, roles y SQL reales.
- Las consultas Git deben hacerse desde `C:\Users\framos\Documents\APIBO\APIBO`.

## Proxima actualizacion sugerida

Cuando terminemos un endpoint:

1. agregarlo a la tabla de `Ya documentados`
2. quitarlo de `Pendientes`
3. actualizar los contadores de estado actual
4. si corresponde, marcar el verbo documentado (`GET`, `POST`, etc.)
