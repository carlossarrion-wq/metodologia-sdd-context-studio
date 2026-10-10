# Credenciales locales de Context Studio

No almacenes credenciales reales en este repositorio ni las publiques en GitHub.

1. Copia `context_studio.credentials.example.json` a `context_studio.credentials.json`.
2. Sustituye los marcadores por los valores emitidos por Context Studio/MCP Gateway.
3. Mantén el fichero real sólo en tu equipo: está excluido mediante `.gitignore`.
4. Configura permisos restrictivos: `chmod 600 credentials/context_studio.credentials.json`.

El fichero esperado contiene:

- `mcp_gateway_url`: URL del endpoint MCP Gateway.
- `mcp_gateway_token`: token para autenticar contra el gateway.
- `context_broker_token`: token de acceso al contexto/Context Broker.

Los tokens compartidos por chat deben considerarse expuestos y revocarse/rotarse antes de utilizarlos.
