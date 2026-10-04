# Edge (em casa)

Dispositivo mínimo sempre ligado na mesma LAN da câmera. Função: subnet router do Tailscale
(e, na Etapa 2, o `ptz-driver`). Nenhuma porta é aberta no roteador.

```bash
curl -fsSL https://tailscale.com/install.sh | sh
echo 'net.ipv4.ip_forward=1' | sudo tee /etc/sysctl.d/99-ts.conf && sudo sysctl -p /etc/sysctl.d/99-ts.conf
sudo tailscale up --advertise-routes=192.168.18.17/32 --accept-dns=false
```
Depois, no painel admin do Tailscale: aprovar a rota anunciada.
Na VPS: `sudo tailscale up --accept-routes`.
