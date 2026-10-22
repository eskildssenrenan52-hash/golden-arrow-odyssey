<!-- LOVABLE:BEGIN -->
> [!IMPORTANT]
> This project is connected to [Lovable](https://lovable.dev). Avoid rewriting
> published git history — force pushing, or rebasing/amending/squashing commits
> that are already pushed — as it rewrites history on Lovable's side and the
> user will likely lose their project history.
>
> Commits you push to the connected branch sync back to Lovable and show up in
> the editor, so keep the branch in a working state.
<!-- LOVABLE:END -->

## Game architecture
- Preserve the imported browser Canvas RPG engine and original catalog; settlement mechanics live in a separate module so combat and quests remain compatible.
- Keep the original browser save key with backward-compatible settlement and terrain fields; this single-player game has no accounts or external database.
- Use deterministic world generation for the expanded biome network so saved terrain coordinates remain stable.
- Match the source game's indexed-access compiler behavior while keeping strict typing enabled; its fixed sprite palettes and tile arrays assume indexed values exist.
- Render sourced world sprites through the browser-only WorldArt module with asset pointers and preserve their CC BY-SA attribution in the public game credits.
- Keep legacy generation and treasure coordinates in the original region; generate frontier terrain in lightweight sectors from a separate browser-safe catalog so larger worlds retain save compatibility and fast startup.
- Size the Canvas camera from its screen bounds and keep controls as accessible overlays; this fills different screen proportions without stretching pixel-art geometry.
- Generate frontier enemies near exploration rather than allocating a world-wide population; this preserves original quest enemies and bounds combat workload.
- World shape, frontier regions, per-biome wall layouts and the road graph live in src/game/landmass.ts and are seeded deterministically, so saved coordinates stay stable across loads.
- A campanha ("A Crônica do Cântico Partido") vive em src/game/story.ts como dados puros: 17 Ecos (um por bioma de fronteira), Vigias (NPCs nos portais), chefes, missões encadeadas, cenas (Beats) e equipamento de fim de jogo. data.ts apenas mescla esses dados em BOSSES/NPCS/QUESTS/ITEMS/RECIPES; não duplique conteúdo da campanha em outros arquivos.
- Chefes e Vigias de fronteira são limpos em world.ts DEPOIS de paintFrontier (que repinta o terreno) e ligados ao portal por uma trilha; src/game/story.test.ts prova por busca em largura que cada um é alcançável. Ao mover arenas, rode os testes.
- Missões aceitam `requires` (uma) e `requiresAll` (todas). Atos 2–5 terminam quando todas as missões em actChainQuests(ato) estão concluídas, o que dispara a cena `act:N`. Cenas são mostradas uma vez (seenBeats é salvo).
- Dificuldade: biomas das duas primeiras fileiras seguem BIOME_LEVEL; mais longe, depthBonus() soma níveis. Cada Ciclo do Eco (New Game+) soma cycleBonus() e reergue chefes mais fortes; o save guarda `cycle` e `seenBeats` e saves antigos continuam válidos.
- Efeitos visuais ficam em engine.ts (postFx: grading por bioma, bloom, vinheta, relâmpagos; godRays; ambientParticles; shadow). Mantêm o pixel-art sem esticar; o bloom só roda se o navegador suportar ctx.filter.

- Game objective guidance lives in src/game/waypoint.ts as pure data logic (tested), the engine only draws it — keeps quest navigation testable without canvas.
- Side questlines (NPCs with chained, dialogue-rich quests) live in src/game/sidequests.ts and are merged in data.ts; keeps them separate from the main Chronicle in story.ts.
