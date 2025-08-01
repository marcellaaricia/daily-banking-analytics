# 🏦 Análise de Performance - Daily Banking
## Projeto de Demonstração de Competências Analíticas

---

## 📋 **RESUMO EXECUTIVO**

Análise completa do comportamento de usuários em plataformas de Daily Banking, utilizando **SQL** para extração de dados, **Excel** para tratamento e análise, e **Power BI** para visualização. 

**Objetivo**: Identificar oportunidades de melhoria na jornada do cliente e otimização de produtos bancários.

---

## 🔍 **METODOLOGIA**

### **1. EXTRAÇÃO DE DADOS (SQL)**
```sql
-- Query principal para análise de transações
SELECT 
    u.user_id,
    u.segment_cliente,
    u.data_cadastro,
    t.tipo_transacao,
    t.canal_acesso,
    t.valor_transacao,
    t.timestamp_transacao,
    t.status_transacao,
    EXTRACT(HOUR FROM t.timestamp_transacao) as hora_transacao,
    EXTRACT(DOW FROM t.timestamp_transacao) as dia_semana
FROM usuarios u
INNER JOIN transacoes t ON u.user_id = t.user_id
WHERE t.timestamp_transacao >= CURRENT_DATE - INTERVAL '30 days'
    AND t.status_transacao = 'SUCESSO'
ORDER BY t.timestamp_transacao DESC;

-- Análise de conversão por funil
WITH funil_conversao AS (
    SELECT 
        DATE(timestamp_transacao) as data,
        COUNT(CASE WHEN tipo_transacao = 'LOGIN' THEN 1 END) as logins,
        COUNT(CASE WHEN tipo_transacao = 'CONSULTA_SALDO' THEN 1 END) as consultas,
        COUNT(CASE WHEN tipo_transacao = 'PIX' THEN 1 END) as transacoes_pix,
        COUNT(CASE WHEN tipo_transacao = 'INVESTIMENTO' THEN 1 END) as investimentos
    FROM transacoes 
    WHERE timestamp_transacao >= CURRENT_DATE - INTERVAL '30 days'
    GROUP BY DATE(timestamp_transacao)
)
SELECT 
    data,
    logins,
    consultas,
    transacoes_pix,
    investimentos,
    ROUND((consultas::DECIMAL / logins) * 100, 2) as taxa_conversao_consulta,
    ROUND((transacoes_pix::DECIMAL / consultas) * 100, 2) as taxa_conversao_pix
FROM funil_conversao
ORDER BY data;
```

### **2. TRATAMENTO DOS DADOS (EXCEL)**

#### **Planilhas Estruturadas:**
- **Sheet1_DadosBrutos**: Dados extraídos via SQL
- **Sheet2_Limpeza**: Tratamento de valores nulos e outliers
- **Sheet3_Métricas**: Cálculos de KPIs e indicadores
- **Sheet4_Segmentação**: Análise por perfil de cliente
- **Sheet5_Dashboard**: Visualizações e gráficos

#### **Fórmulas Excel Utilizadas:**
```excel
# Taxa de Conversão
=SE(B2<>0;C2/B2;"")

# Segmentação de Clientes por Valor
=SE(D2>=10000;"Premium";SE(D2>=5000;"Gold";"Standard"))

# Análise de Sazonalidade (Média Móvel)
=MÉDIA(E2:E8)

# Identificação de Outliers
=SE(ABS(F2-MÉDIA($F$2:$F$100))>2*DESVPAD($F$2:$F$100);"OUTLIER";"OK")

# Ranking de Produtos
=CLASSIFICAR(G2;$G$2:$G$50;0)
```

---

## 📊 **PRINCIPAIS DESCOBERTAS**

### **KPIs Analisados:**
| Métrica | Valor Atual | Meta | Status |
|---------|-------------|------|---------|
| **Usuários Ativos (MAU)** | 2.3M | 2.5M | 🔴 -8% |
| **Taxa Conversão Pix** | 87.5% | 85% | 🟢 +2.5% |
| **NPS Score** | 4.2 | 4.0 | 🟢 +5% |
| **Tempo Resposta Médio** | 1.8s | 1.5s | 🔴 +20% |
| **Revenue per User** | R$ 42.30 | R$ 40.00 | 🟢 +5.7% |

### **Insights Identificados:**

#### 🎯 **1. Comportamento Temporal**
- **Peak de uso**: 18h-20h (35% do volume diário)
- **Menor atividade**: 2h-6h (apenas 3% do volume)
- **Dia da semana**: Segunda-feira tem 23% mais transações

#### 💡 **2. Análise de Produtos**
- **Pix**: 45% das transações (crescimento de 15% vs mês anterior)
- **TED**: 20% das transações (queda de 5%)
- **Investimentos**: Apenas 8% dos usuários ativos utilizam

#### ⚠️ **3. Pontos de Atenção**
- **Abandono de sessão**: 23% dos usuários saem após consulta de saldo
- **Tempo de resposta**: API de investimentos 40% mais lenta
- **Canais**: App mobile representa 89% dos acessos

---

## 🚀 **RECOMENDAÇÕES ESTRATÉGICAS**

### **Curto Prazo (1-2 meses):**
1. **Otimizar APIs críticas** - Reduzir tempo de resposta em 25%
2. **Campanhas direcionadas** - Push notifications no horário peak
3. **A/B Test na jornada** - Simplificar fluxo de investimentos

### **Médio Prazo (3-6 meses):**
1. **Programa de retenção** - Foco em usuários que abandonam após consulta
2. **Cross-sell inteligente** - IA para sugestões personalizadas
3. **Gamificação** - Sistema de pontos para engajamento

### **Longo Prazo (6+ meses):**
1. **Open Banking** - Integração com outras instituições
2. **Super App** - Expansão para serviços além banking
3. **Análise preditiva** - ML para prevenção de churn

---

## 🛠️ **FERRAMENTAS UTILIZADAS**

| Ferramenta | Uso | Justificativa |
|------------|-----|---------------|
| **PostgreSQL** | Extração e transformação de dados | Queries complexas e performance |
| **Excel** | Análise exploratória e cálculos | Flexibilidade e facilidade de uso |
| **Power BI** | Dashboards executivos | Visualizações interativas |
| **Python** | Análises estatísticas avançadas | Machine Learning e correlações |

---

## 📈 **IMPACTO ESPERADO**

### **Métricas de Sucesso:**
- ✅ **Aumento de 12%** na taxa de conversão para investimentos
- ✅ **Redução de 30%** no tempo de resposta médio
- ✅ **Crescimento de 8%** no MAU (Usuários Ativos Mensais)
- ✅ **Melhoria de 15%** no NPS Score

### **ROI Estimado:**
- **Investimento**: R$ 450K (desenvolvimento + recursos)
- **Retorno projetado**: R$ 1.2M (12 meses)
- **ROI**: 167% no primeiro ano

---

## 📝 **PRÓXIMOS PASSOS**

1. **Validação com stakeholders** - Apresentar findings para POs
2. **Priorização no backlog** - Definir roadmap de implementação  
3. **Setup de monitoramento** - Dashboards em tempo real
4. **Testes A/B** - Validar hipóteses com usuários reais
5. **Documentação técnica** - Histórias de usuário detalhadas

---

## 📋 **ANEXOS**

### **Queries SQL Utilizadas:**
- `analise_transacoes.sql` - Extração de dados principais
- `funil_conversao.sql` - Análise de conversão por etapas
- `segmentacao_clientes.sql` - Classificação por perfil
- `performance_apis.sql` - Monitoramento de tempos

### **Planilhas Excel:**
- `dados_brutos_30d.xlsx` - Dataset principal
- `analise_exploratoria.xlsx` - Estatísticas descritivas  
- `metricas_kpi.xlsx` - Indicadores de performance
- `dashboard_executivo.xlsx` - Visão consolidada

### **Dashboards Power BI:**
- `daily_banking_overview.pbix` - Visão geral operacional
- `customer_journey.pbix` - Análise de jornadas
- `product_performance.pbix` - Performance por produto

---

*Desenvolvido por [Seu Nome] como demonstração de competências analíticas para oportunidades em Product Analytics no setor bancário.*