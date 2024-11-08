# Criação do Cluster Kubernetes com uma Conta de Serviço Restrita
# Requisito: O cluster deve ser implantado com uma conta de serviço dedicada e configurada com o mínimo de privilégios exigidos.
# O cluster deve ser privado, com endpoint público desativado, e a rede autorizada para o mestre deve incluir apenas o IP do orca-jumphost.

gcloud projects add-iam-policy-binding qwiklabs-gcp-03-087074797bda \
  --member="serviceAccount:orca-private-cluster-533-sa@qwiklabs-gcp-03-087074797bda.iam.gserviceaccount.com" \
  --role="roles/logging.logWriter"

# Esse comando atribui ao service account o papel roles/logging.logWriter, permitindo gravar logs necessários no Google Cloud Logging, atendendo o requisito de mínima permissão necessária para o cluster. Essa é uma das permissões mínimas necessárias (segundo o guia de segurança do GKE) que devem ser atribuídas ao service account para acessar o cluster.

gcloud projects add-iam-policy-binding qwiklabs-gcp-03-087074797bda \
  --member="serviceAccount:orca-private-cluster-533-sa@qwiklabs-gcp-03-087074797bda.iam.gserviceaccount.com" \
  --role="roles/logging.logWriter"

# Esse comando concede uma função personalizada ao service account com as permissões para acessar e manipular objetos no Google Cloud Storage. A função personalizada contém permissões específicas para storage.buckets.get, storage.objects.get, storage.objects.list, storage.objects.update, e storage.objects.create, conforme exigido pelo desenvolvimento da equipe.

gcloud projects add-iam-policy-binding qwiklabs-gcp-03-087074797bda \
  --member="serviceAccount:orca-private-cluster-533-sa@qwiklabs-gcp-03-087074797bda.iam.gserviceaccount.com" \
  --role="projects/qwiklabs-gcp-03-087074797bda/roles/orca_storage_editor_670"

# Implantação do Cluster Kubernetes como um Cluster Privado
# Requisito: O cluster deve ser implantado como um cluster privado na sub-rede orca-build-subnet da VPC orca-build-vpc.

gcloud container clusters create orca-cluster-155 \
  --zone="$ZONE" \
  --network orca-build-vpc \
  --subnetwork orca-build-subnet \
  --service-account orca-private-cluster-533-sa@qwiklabs-gcp-03-087074797bda.iam.gserviceaccount.com \
  --enable-master-authorized-networks \
  --enable-ip-alias \
  --enable-private-nodes \
  --enable-private-endpoint

# Configuração da Rede Autorizada
# Requisito: Apenas o orca-jumphost deve ter acesso ao endpoint do cluster, e o IP autorizado do mestre deve ser configurado para incluir o endereço IP interno do orca-jumphost.

gcloud container clusters update orca-cluster-155 \
  --enable-master-authorized-networks \
  --master-authorized-networks=192.168.10.2/32 --zone="$ZONE"


# Acesso ao Jump Host e Configuração do kubectl para o Cluster
# Requisito: A conexão deve ser feita a partir do orca-jumphost para acessar o cluster. Deve-se usar o IP interno para se conectar ao cluster, pois ele é um cluster privado.

gcloud compute ssh orca-jumphost --zone="$ZONE"

gcloud container clusters get-credentials orca-cluster-155 --internal-ip --project=qwiklabs-gcp-03-087074797bda --zone="$ZONE"

# Instalação do Plugin de Autenticação para GKE
# Requisito: Certificar-se de que o ambiente kubectl no jump host está pronto para autenticar e se comunicar com o cluster privado

sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin
echo "export USE_GKE_GCLOUD_AUTH_PLUGIN=True" >> ~/.bashrc
source ~/.bashrc

# Implantação de uma Aplicação de Teste para Validação do Cluster
# Requisito: Validar se o cluster está configurado corretamente ao implantar uma aplicação simples e testá-la usando o kubectl.

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
