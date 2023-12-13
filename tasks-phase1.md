IMPORTANT ❗ ❗ ❗ Please remember to destroy all the resources after each work session. You can recreate infrastructure by creating new PR and merging it to master.
  
![img.png](doc/figures/destroy.png)


1. Authors:

   ***enter your group nr***

   ***link to forked repo***
   
2. Fork https://github.com/bdg-tbd/tbd-2023z-phase1 and follow all steps in README.md.

3. Select your project and set budget alerts on 5%, 25%, 50%, 80% of 50$ (in cloud console -> billing -> budget & alerts -> create buget; unclick discounts and promotions&others while creating budget).

  ![img.png](doc/figures/discounts.png)

4. From avaialble Github Actions select and run destroy on main branch.

5. Create new git branch and add two resources in ```/modules/data-pipeline/main.tf```:
    1. resource "google_storage_bucket" "tbd-data-bucket" -> the bucket to store data. Set the following properties:
        * project  // look for variable in variables.tf
        * name  // look for variable in variables.tf
        * location // look for variable in variables.tf
        * uniform_bucket_level_access = false #tfsec:ignore:google-storage-enable-ubla
        * force_destroy               = true
        * public_access_prevention    = "enforced"
        * if checkcov returns error, add other properties if needed
       
    2. resource "google_storage_bucket_iam_member" "tbd-data-bucket-iam-editor" -> assign role storage.objectUser to data service account. Set the following properties:
        * bucket // refere to bucket name from tbd-data-bucket
        * role   // follow the instruction above
        * member = "serviceAccount:${var.data_service_account}"

          ***insert the link to the modified file and terraform snippet here***
          
              resource "google_storage_bucket" "tbd-data-bucket" {
                project                     = var.project_name
                name                        = var.data_bucket_name
                location                    = var.region
                uniform_bucket_level_access = false #tfsec:ignore:google-storage-enable-ubla
                force_destroy               = true
                public_access_prevention    = "enforced"
                # Add other properties if needed to handle errors from checkcov
                  versioning {
                  enabled = true
                }
              }
              
              resource "google_storage_bucket_iam_member" "tbd-data-bucket-iam-editor" {
                bucket = google_storage_bucket.tbd-data-bucket.name
                role   = "roles/storage.objectUser"
                member = "serviceAccount:${var.data_service_account}"
              }

    Create PR from this branch to **YOUR** master and merge it to make new release. 
    
    ***place the screenshot from GA after succesfull application of release with this changes***
                  ![5](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/23d8f491-223c-40f8-a6ef-c30e3e33ac9e)



6. Analyze terraform code. Play with terraform plan, terraform graph to investigate different modules.

    ***describe one selected module and put the output of terraform graph for this module here***
   
       Wybralismy moduł dataproc ze względu na wielkość grafu. Dla innych modułów ilośc zalezności przykrywała jego czytelność.
       Kilka słów omówienie:
       Usługa Dataproc jest włączona dla określonego projektu Google Cloud (var.project_name). Wykorzystuje dostawcę google i docelowo usługę dataproc.googleapis.com.
       Ten zasób definiuje klaster Dataproc o nazwie "tbd-cluster" w określonym projekcie GCP i regionie (var.project_name i var.region odpowiednio). Zależy od udanego utworzenia usługi Dataproc (google_project_service.dataproc).
        W bloku cluster_config znajdują się konfiguracje klastra Dataproc:
       Określam wersję obrazu Dataproc do użycia. Wykorzystuje wartość określoną w zmiennej var.image_version.
        Konfiguruje ustawienia Google Compute Engine (GCE) dla klastra. Ustawia podsieć, zezwala tylko na wewnętrzne adresy IP var.subnet
       
      ![6](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/13f909d7-ec70-425d-ac9e-6a9c8b6498eb)


   
   
8. Reach YARN UI
   
   ***place the port and the screenshot of YARN UI here***
   
9. Draw an architecture diagram (e.g. in draw.io) that includes:
    1. VPC topology with service assignment to subnets
    2. Description of the components of service accounts

            Composer - w pełni zarządzana usługa orkiestracji przepływu pracy oparta na Apache Airflow, która pomaga tworzyć, planować i monitorować pipelines obejmujące środowiska hybrydowe i wielochmurowe.
            vpc - Prywatne środowisko wirtualne w chmurze (VPC) Google Cloud Platform (GCP) zapewnia obsługę sieci w zasobach, takich jak instancje maszyn wirtualnych Compute Engine, kontenery Kubernetes Engine czy elastyczne środowisko App Engine.
            Dataproc - Dataproc to w pełni zarządzana i wysoce skalowalna usługa do uruchamiania Apache Hadoop, Apache Spark, Apache Flink, Presto oraz ponad 30 narzędzi i frameworków open source
            Artifical registry - Rejestr kontenerów nowej generacji. Przechowuje, zarządza i zabezpiecza artefakty kompilacji.
            cloud storage - Cloud Storage to zarządzana usługa przechowywania nieustrukturyzowanych danych.
            IAM - Zarządzanie tożsamością i dostępem (IAM) pozwala administratorom autoryzować, kto może podejmować działania na określonych zasobach, zapewniając pełną kontrolę i widoczność w celu centralnego zarządzania zasobami Google Cloud.
       
    3. List of buckets for disposal
       
            tbd-2023z-30047812-code    
            tbd-2023z-30047812-conf    
            tbd-2023z-30047812-data    
            tbd-2023z-30047812-state
       
    4. Description of network communication (ports, why it is necessary to specify the host for the driver) of Apache Spark running from Vertex AI Workbech

  
    ***place your diagram here***
   
   ![9](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/6ee2f712-e062-4673-8f3d-b893e0d7cb11)


11. Add costs by entering the expected consumption into Infracost

         Niestety nie udało nm się wygenerować pełnego pliku infracost ale widzimy w poniższym obrazku koszt dla poszczególnych elementów infrastruktóry.
         
   ![image](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/3ae995f5-b689-4256-920c-f67617c7a3cb)


11. Some resources are not supported by infracost yet. Estimate manually total costs of infrastructure based on pricing costs for region used in the project. Include costs of cloud composer, dataproc and AI vertex workbanch and them to infracost estimation.

        Composer

    ![image](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/7ecb280c-11b6-4af1-8325-b7d1718d94b3)

        Dataproc

    ![image](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/3cdec0f8-d142-4ebe-8722-e705c6e2ec55)

        AI vertex workbanch

    ![image](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/e24a4ceb-f35d-4661-a645-6bd639fe5115)

  
13. Create a BigQuery dataset and an external table
    
          Dodaliśmy tabelę ręcznie
    ![image](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/d066dc47-3818-466c-965a-0e718acfc0f7)

  
15. Start an interactive session from Vertex AI workbench (steps 7-9 in README):


          Dostaliśmy dokładnie tak samo jak w readme
    ![image](https://github.com/Pawel-Barej/tbd-2023z-phase1/assets/89931555/a2aeb39c-76e0-4d97-8cd5-1d0caebd2fb8)

   
16. Find and correct the error in spark-job.py

        Powodem była zmiana lokalizacji bucketu

17. Additional tasks using Terraform:

    1. Add support for arbitrary machine types and worker nodes for a Dataproc cluster and JupyterLab instance

    ***place the link to the modified file and inserted terraform code***
    
    3. Add support for preemptible/spot instances in a Dataproc cluster

    ***place the link to the modified file and inserted terraform code***
    
    3. Perform additional hardening of Jupyterlab environment, i.e. disable sudo access and enable secure boot
    
    ***place the link to the modified file and inserted terraform code***

    4. (Optional) Get access to Apache Spark WebUI

    ***place the link to the modified file and inserted terraform code***
