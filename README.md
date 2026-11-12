<table border="0">
  <tr>
    <td valign="middle">
      <img src="docs/icon.png" alt="Outbound App logo" width="96" />
    </td>
    <td valign="middle">
      <h1>Outbound Comms Analysis</h1>
      <img src="https://img.shields.io/github/last-commit/filippospol/outbound-app" alt="Last commit" />
      <img src="https://github.com/filippospol/outbound-app/actions/workflows/outbound-comms-analysis-data.yml/badge.svg" alt="Pipeline status" />
    </td>
  </tr>
</table>

Outbound App is where the Outbound team's call logs turn into answers. An R Shiny dashboard tracks every campaign as it happens — who was called, who picked up, how each agent and campaign is performing — with the full history a filter away. Behind it, a GitHub Actions pipeline runs daily: it pulls the latest call logs, cleans and validates them (with a data-quality guard that flags bad imports before anyone has to find them the hard way), and publishes a ready-to-load data bundle the app picks up on the next refresh. A Rebuild button in the app kicks off the same pipeline on demand, so the numbers are never more than a few minutes behind the sheet. It's the team's single view of the calling operation: the campaigns, the agents, the calls worth making next.

## License

MIT, see [LICENSE](LICENSE).
