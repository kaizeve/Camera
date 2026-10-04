# Etapa 1 – Frigate na VPS

## Preparar
1. Edge e VPS no Tailscale (ver `edge/README.md`; rota 192.168.18.17/32 aprovada; VPS com `--accept-routes`).
2. `cp .env.example .env && chmod 600 .env` e preencha `TAILSCALE_IP` (`tailscale ip -4`) e as credenciais.
3. `cp frigate/config.yml.example frigate/config.yml`.
4. Firewall da VPS: `ufw default deny incoming; ufw allow OpenSSH; ufw enable` (ou SSH só via Tailscale).

## Testes
```bash
# câmera visível pela VPS? (UDP)
ffprobe -rtsp_transport udp "rtsp://USER:PASS@192.168.18.17:554/onvif1"
docker compose config >/dev/null && docker compose up -d
docker compose logs -f frigate      # senha do admin inicial aparece no log
```
- UI: `https://<TAILSCALE_IP>:8971` (certificado autoassinado), login obrigatório. Live deve aparecer.
- Falha esperada de fora da tailnet: `curl -m3 http://<IP_PUBLICO>:8971`, `:5000`, `:1984` → sem resposta.
- Mova algo na frente da câmera: surge segmento em `storage/recordings` e evento na UI.

## Retenção
Edite `record.*.days` em `frigate/config.yml`. `continuous.days: 0` = não grava contínuo.

## Pontos a validar na 1ª subida
- Schema de `record` da sua versão (0.17.x) e se `ffmpeg:cam_hevc#video=h264` funciona no go2rtc.
- Uso de CPU da transcodificação (se alto: baixar `detect.fps` ou transcodificar no edge).
