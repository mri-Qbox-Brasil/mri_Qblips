# MANUAL - mri_Qblips

## O que o recurso faz (descrição funcional)
Sistema avançado de gerenciamento de blips para FiveM, com interface React NUI. Permite criação, edição e remoção de blips no mapa, com restrições de acesso por job/gangue e persistência em banco de dados.

## Funcionalidades principais
- **Gerenciamento completo de blips**: Criação, seleção de sprite, personalização de cor/escala/opacidade, blips piscantes, curto alcance, ocultação dinâmica.
- **Controle de acesso**: Restrição por job, gangue ou grupos ACE.
- **Interface moderna**: React NUI com pré-visualização em tempo real, busca/filtro, operações em massa.
- **Persistência**: Blips salvos em MySQL, persistem após reinícios.

## Como funciona (fluxo de trabalho)
1. **Acesso**: Jogador com permissão ACE (`command.blipcreator`) usa comando `/blip`.
2. **Interface NUI**: Abre React NUI com lista de blips existentes.
3. **Criação de blip**: Clica em "Criar", define nome, coordenadas, sprite, cor, escala, opacidade, flash, shortRange, job/gang.
4. **Edição/remoção**: Seleciona blip existente, edita propriedades ou deleta.
5. **Sync**: Alterações são salvas no DB e sincronizadas com todos os clients.

## Opções de configuração disponíveis
Configurações em `config/config.lua`:

| Opção | Padrão | Descrição |
|-------|--------|-----------|
| `Config.ACE_Permission` | `'command.blipcreator'` | Permissão ACE para usar o comando. |
| `Config.DefaultBlip.sprite` | `1` | Sprite padrão de blip. |
| `Config.DefaultBlip.color` | `0` | Cor padrão de blip. |
| `Config.DefaultBlip.scale` | `0.8` | Escala padrão de blip. |
| `Config.DefaultBlip.opacity` | `255` | Opacidade padrão de blip. |
| `Config.AllowedGroups` | `{'admin', 'moderator'}` | Grupos com acesso (além de ACE). |

## Comandos disponíveis
| Comando | Permissão | Descrição |
|---------|------------|-------------|
| `/blip` | ACE: `command.blipcreator` | Abre menu de gerenciamento de blips. |

## Eventos que dispara/ouve

### Cliente → Servidor
| Evento | Parâmetros | Descrição |
|--------|------------|-----------|
| `mri_Qblips:client:openMenu` | Nenhum | Abre UI de gerenciamento. |
| `mri_Qblips:client:createBlip` | `blipData` | Cria novo blip. |
| `mri_Qblips:client:updateBlip` | `id, blipData` | Atualiza blip existente. |
| `mri_Qblips:client:deleteBlip` | `id` | Deleta blip. |
| `mri_Qblips:client:refreshBlips` | Nenhum | Atualiza todos os blips. |

### Servidor → Cliente
| Evento | Parâmetros | Descrição |
|--------|------------|-----------|
| `mri_Qblips:server:saveBlip` | `blipData` | Salva blip no DB. |
| `mri_Qblips:server:updateBlip` | `id, blipData` | Atualiza blip no DB. |
| `mri_Qblips:server:deleteBlip` | `id` | Deleta blip do DB. |
| `mri_Qblips:server:requestBlips` | Nenhum | Solicita todos os blips do DB. |

## Exports que fornece/consome

### Exports do cliente
| Export | Parâmetros | Descrição |
|--------|------------|-----------|
| `createBlip` | `blipData` | Cria blip com nome, coords, sprite, cor, etc. |
| `getBlips` | Nenhum | Obtém todos os blips. |
| `deleteBlip` | `blipId` | Deleta blip por ID. |

### Exports do servidor
| Export | Parâmetros | Descrição |
|--------|------------|-----------|
| `saveBlip` | `source, blipData` | Salva blip no DB. |
| `getAllBlips` | Nenhum | Obtém todos os blips do DB. |
| `deleteBlip` | `blipId` | Deleta blip do DB. |

### Exports consumidos
Nenhum export externo consumido diretamente, usa oxmysql para persistência.

## Integração com outros recursos MRI Qbox
- **oxmysql**: Persistência de dados de blips.
- **ox_lib**: Componentes UI e notificações.
- **Outros recursos**: Podem usar exports para criar blips dinamicamente (ex: mri_Qfarm cria blip de fazenda).

## Casos de uso / exemplos práticos
1. **Criação de blip de hospital**: Admin usa `/blip`, cria blip com sprite 61 (hospital), cor 2, coords do hospital.
2. **Restrição de blip para polícia**: Blip de delegacia restrito a job `police`, outros jobs não veem.
3. **Blip de evento temporário**: Admin cria blip de evento, define `hidden = false`, após evento deleta ou oculta.
4. **Blip de gangue**: Blip de esconderijo da gangue `ballas`, restrito a membros da gangue.

## Dicas de solução de problemas
- **Blip não aparece**: Verifique se o jogador tem permissão para ver o blip (job/gang/ACE).
- **Interface não abre**: Confirme que o jogador tem permissão ACE `command.blipcreator`.
- **Erros de banco de dados**: Importe o arquivo SQL `db/blips.sql` e verifique se oxmysql está instalado.
- **Blips não persistem**: Verifique se o DB está salvando corretamente e o servidor está sincronizando com clients.
- **Sprite inválido**: Use IDs de sprite válidos do GTA V (referência no README.md).
