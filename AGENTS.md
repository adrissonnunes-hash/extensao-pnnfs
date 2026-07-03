# Instruções para o Codex — Livros ISS.Net Santa Maria

## Projeto

Projeto Python/Tkinter + Playwright que automatiza o ISS.Net de Santa Maria usando um único login manual do contador e a mesma sessão para várias empresas.

Versão de origem: **32.0.0**.

## Prioridade atual

O processamento será usado com muitos CNPJs. Otimize fortemente, sem alterar as regras funcionais já validadas:

1. identificação dos CNPJs no portal;
2. troca entre empresas;
3. identificação das pastas iniciais dos clientes em `O:\`;
4. geração e salvamento dos PDFs oficiais;
5. redução de esperas fixas, navegações repetidas e leituras completas desnecessárias.

## Regra obrigatória das pastas dos clientes

O programa deve localizar uma pasta já existente diretamente em `O:\` e validar o caminho padrão:

`O:\Pasta da Empresa\Dpto Tributário\AAAA\Arquivos de Fechamento\Fechamento ICMS IPI ISSQN`

Somente é permitido criar a pasta final da competência `MMAAAA`, por exemplo `062026`.

Nunca criar, renomear, mover ou excluir:

- a pasta inicial do cliente;
- `Dpto Tributário`;
- o ano;
- `Arquivos de Fechamento`;
- `Fechamento ICMS IPI ISSQN`;
- qualquer outra pasta existente.

Se o caminho anterior à competência não existir, não salvar e não criar estrutura intermediária.

Matriz e filial devem ser identificadas pelo CNPJ completo. Nunca vincular filial à matriz automaticamente.

## Estratégia exigida para acelerar pastas

- Fazer apenas uma varredura de primeiro nível em `O:\` por execução.
- Montar um índice em memória com nome original, nome normalizado, tokens e caminho.
- Não executar `os.listdir`, `glob`, `rglob` ou `Path.iterdir()` para cada CNPJ.
- Reutilizar o mapeamento persistente por CNPJ completo.
- Validar mapeamentos persistentes com `Path.is_dir()` e caminho padrão, sem revarrer a unidade.
- Calcular correspondência aproximada apenas entre candidatos pré-filtrados por tokens fortes.
- Não escolher automaticamente quando houver ambiguidade.
- Permitir seleção manual uma vez e memorizar o vínculo fora da unidade `O:\`.

## Regras do portal

- Login, verificação de segurança e eventual CAPTCHA são sempre manuais.
- Manter uma única sessão e uma única guia principal.
- Não depender de foco da janela, clique manual em Salvar ou visualizador do Edge.
- Prestados: `Livro Fiscal -> Emitir Livro Fiscal`, de 01/01 até o fim do mês escolhido.
- Tomados: `Declaração de Serviços Contratados -> Consulta de Notas Tomadas`, somente o mês escolhido.
- Cancelamentos: consultar a faixa da primeira à última NFS-e emitida no mês, manter Status em `Selecione` e gerar um único TXT apenas com Deferidas e Indeferidas.

## Qualidade

- Preservar compatibilidade com Windows e geração de EXE.
- Não remover fallbacks que protegem contra diferenças do portal sem testes equivalentes.
- Criar testes de desempenho determinísticos para muitos CNPJs e muitas pastas.
- Todos os testes existentes devem continuar passando.
- Registrar métricas no log: tempo de índice das pastas, tempo médio por empresa, cache hits e fallbacks.

## Resultado esperado

Entregar alterações pequenas e testáveis, com uma comparação antes/depois do número de acessos ao disco, navegações no portal e tempo estimado para lotes grandes.
