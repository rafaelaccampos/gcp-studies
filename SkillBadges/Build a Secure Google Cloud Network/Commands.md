# Remove uma regra de firewall chamada open-access, que pode estar permitindo acesso excessivo e indesejado.
gcloud compute firewall-rules delete open-access

# Remove uma regra de firewall chamada allow-ssh-iap-ingress-ql-101, que pode estar configurada incorretamente.
gcloud compute firewall-rules delete allow-ssh-iap-ingress-ql-101

# Inicia a instância bastion na zona us-east4-a.
gcloud compute instances start bastion --zone=us-east4-a

# Adiciona a tag allow-ssh-iap-ingress-ql-101 à instância bastion para permitir SSH via IAP.
gcloud compute instances add-tags bastion --tags allow-ssh-iap-ingress-ql-101 --zone us-east4-a

# Adiciona a tag allow-http-ingress-ql-101 à instância juice-shop para permitir acesso HTTP ao público.
gcloud compute instances add-tags juice-shop --tags allow-http-ingress-ql-101 --zone us-east4-a

# Cria uma regra de firewall para permitir SSH na porta 22, acessível apenas pelo range de IPs do IAP (35.235.240.0/20).
# Aplica-se apenas a VMs com a tag allow-ssh-iap-ingress-ql-101 (aplicada anteriormente ao bastion).
gcloud compute firewall-rules create allow-sh-isap-ingress-ql-101 --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags allow-ssh-iap-ingress-ql-101 --network acme-vpc

# Cria uma regra de firewall que permite HTTP na porta 80, de qualquer endereço IP (0.0.0.0/0).
# Aplica-se apenas a VMs com a tag allow-http-ingress-ql-101 (aplicada anteriormente ao juice-shop).
gcloud compute firewall-rules create allow-http-ingress-ql-101 --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags allow-http-ingress-ql-101 --network acme-vpc

# Cria uma regra de firewall para permitir SSH na porta 22, a partir de um range de IPs internos (192.168.10.0/24).
# Aplica-se apenas a VMs com a tag allow-ssh-internal-ingress-ql-101 (aplicada anteriormente ao juice-shop).
gcloud compute firewall-rules create allow-ssh-internal-ingress-ql-101 --allow=tcp:22  --source-ranges 192.168.10.0/24 --target-tags allow-ssh-internal-ingress-ql-101 --network acme-vpc

# Conecta-se ao bastion via SSH usando o IAP.
gcloud compute ssh bastion --zone=us-east4-a --tunnel-through-iap

# Uma vez conectado ao bastion, use SSH para acessar o Juice-Shop internamente.
ssh ip-juice-shop
