# M&A DD — 標準列セット

買い手側ターゲット契約レビューのデフォルトスキーマ。ここから始め、ディールに応じて列を追加・削除。これは出発点であってチェックリストではない — 株式譲渡契約の reps とリクエストリストが実際に重要なものを駆動。

```yaml
schema:
  name: "M&A Diligence — Standard"
  columns:
    - id: counterparty
      label: "Counterparty"
      type: verbatim
      prompt: "Name the contracting party other than the target entity, exactly as it appears."

    - id: agreement_type
      label: "Agreement Type"
      type: classify
      options: [msa, purchase_order, license_in, license_out, lease, services, supply, distribution, nda, joint_venture, loan, guaranty, employment, other]
      prompt: "What kind of agreement is this?"

    - id: effective_date
      label: "Effective Date"
      type: date
      prompt: "When did this agreement become effective?"

    - id: term
      label: "Term"
      type: duration
      prompt: "What is the initial term?"

    - id: auto_renewal
      label: "Auto-Renewal"
      type: classify
      options: [none, annual, fixed_period, evergreen]
      prompt: "Does the agreement auto-renew? On what cycle?"

    - id: termination_for_convenience
      label: "Termination for Convenience"
      type: classify
      options: [none, either_party, target_only, counterparty_only]
      prompt: "Can either party terminate without cause? Who?"

    - id: termination_notice
      label: "Termination Notice Period"
      type: duration
      prompt: "How much notice is required to terminate?"

    - id: change_of_control
      label: "Change of Control"
      type: classify
      options: [silent, consent_required, consent_not_unreasonably_withheld, automatic_termination, notice_only, counterparty_right_to_terminate]
      prompt: "Does the agreement address a change of control of the target? What triggers and what happens?"

    - id: assignment
      label: "Assignment"
      type: classify
      options: [silent, consent_required, consent_not_unreasonably_withheld, freely_assignable, assignable_to_affiliates, non_assignable]
      prompt: "Can the target assign this agreement? What restrictions apply?"

    - id: exclusivity
      label: "Exclusivity / Non-Compete"
      type: classify
      options: [none, exclusive_supplier, exclusive_customer, non_compete, non_solicit, territory_restriction, most_favored_nation]
      prompt: "Does the agreement restrict either party from competing or contracting with others?"

    - id: liability_cap
      label: "Liability Cap"
      type: currency
      prompt: "Is there a cap on liability? What is the amount or multiplier?"

    - id: indemnification
      label: "Indemnification"
      type: classify
      options: [none, mutual, target_indemnifies, counterparty_indemnifies, ip_only, third_party_claims_only]
      prompt: "Who indemnifies whom, and for what?"

    - id: governing_law
      label: "Governing Law"
      type: verbatim
      prompt: "What jurisdiction's law governs?"

    - id: dispute_resolution
      label: "Dispute Resolution"
      type: classify
      options: [litigation, arbitration_binding, arbitration_nonbinding, mediation_first, silent]
      prompt: "How are disputes resolved?"

    - id: most_favored_nation
      label: "MFN / Pricing Protection"
      type: classify
      options: [none, mfn_pricing, price_matching, benchmarking_right]
      prompt: "Is there a most-favored-nation or pricing protection clause?"

    - id: minimum_commitments
      label: "Minimum Purchase / Volume Commitments"
      type: currency
      prompt: "Are there minimum purchase, volume, or spend commitments?"

    - id: ip_ownership
      label: "IP Ownership"
      type: classify
      options: [each_owns_own, target_owns_work_product, counterparty_owns_work_product, joint, license_only, silent]
      prompt: "Who owns intellectual property created or used under the agreement?"

    - id: confidentiality_term
      label: "Confidentiality Survival"
      type: duration
      prompt: "How long do confidentiality obligations survive termination?"

    - id: insurance_requirements
      label: "Insurance Requirements"
      type: classify
      options: [none, general_liability, professional_liability, cyber, workers_comp, umbrella]
      prompt: "What insurance must be maintained?"

    - id: audit_rights
      label: "Audit Rights"
      type: classify
      options: [none, counterparty_may_audit_target, target_may_audit_counterparty, mutual]
      prompt: "Does either party have audit rights?"

    - id: notices
      label: "Notice Requirements"
      type: verbatim
      prompt: "What is the notice address and method for the target?"
```

## ディールタイプ別の一般的な追加

- **テック / IP 集約型ターゲット:** ソースコード・エスクロー、オープンソース制限、データ権、モデル学習権、API アクセス
- **ヘルスケア / ライフサイエンス:** BAA(Business Associate Agreement)の有無、規制届出義務、FDA とのやり取り、臨床試験義務
- **政府契約者:** novation consent 要件、flow-down 条項、セキュリティクリアランス、FAR / DFARS 引用
- **不動産:** 更新オプション、賃料エスカレーション、CAM 規定、サブオーディネーション、エストッペル要件
- **規制対象金融:** 規制承認条件、資本要件、FINRA / SEC 申請トリガー

## 高速初回パスでの一般的なカット

時間制約のある初期スクリーンには、次の 6 列が早期ディール質問の 80% に答える:counterparty、effective_date、term、change_of_control、assignment、termination_for_convenience。最初にこれらを実行、ディールチームが優先順位を付けたらスキーマを拡張。
