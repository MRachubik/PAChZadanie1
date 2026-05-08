# Zadanie 1 (Część nieobowiązkowa)

## Analiza podatności na zagrożenia (CVE)

Komenda:

```bash
docker scout quickview mrcellar/lab_pogoda:latest
```

Wynik:

```bash
    ✓ Pulled
    ✓ Image stored for indexing
    ✓ Indexed 81 packages
    ✓ Provenance obtained from attestation

    i Base image was auto-detected. To get more accurate results, build images with max-mode provenance attestations.
      Review docs.docker.com ↗ for more information.

 Target             │  mrcellar/lab_pogoda:latest  │    0C     1H     4M     2L  
   digest           │  aecaf7cff851                │                             
 Base image         │  python:3.11-alpine          │    0C     1H     4M     2L  
 Updated base image │  python:3.14-alpine          │    0C     0H     3M     1L  
                    │                              │           -1     -1     -1  
```

## Dokładne informacje o zagrożeniach:

Komenda:

```bash
docker scout cves mrcellar/lab_pogoda:latest
```

Wynik:

```bash
## Overview

                   │               Analyzed Image               
───────────────────┼────────────────────────────────────────────
 Target            │  mrcellar/lab_pogoda:latest                
   digest          │  aecaf7cff851                              
   platform        │ linux/amd64                                
   provenance      │ git@github.com:MRachubik/PAChZadanie1.git  
                   │  810d7d89bc0cf74810ba7819e9e72b2b72614b59  
   vulnerabilities │    0C     1H     4M     2L                 
   size            │ 23 MB                                      
   packages        │ 81                                         


## Packages and Vulnerabilities

   0C     1H     0M     0L  wheel 0.45.1
pkg:pypi/wheel@0.45.1

    ✗ HIGH CVE-2026-24049 [Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')]
      https://scout.docker.com/v/CVE-2026-24049
      Affected range : >=0.40.0                                     
                     : <=0.46.1                                     
      Fixed version  : 0.46.2                                       
      CVSS Score     : 7.1                                          
      CVSS Vector    : CVSS:3.1/AV:L/AC:L/PR:N/UI:R/S:U/C:N/I:H/A:H 
    

   0C     0H     3M     1L  pip 24.0
pkg:pypi/pip@24.0

    ✗ MEDIUM CVE-2025-8869 [Improper Link Resolution Before File Access ('Link Following')]
      https://scout.docker.com/v/CVE-2025-8869
      Affected range : <=25.2                                                          
      Fixed version  : 25.3                                                            
      CVSS Score     : 5.9                                                             
      CVSS Vector    : CVSS:4.0/AV:N/AC:L/AT:P/PR:N/UI:A/VC:N/VI:H/VA:N/SC:N/SI:N/SA:N 
    
    ✗ MEDIUM CVE-2026-6357 [Inclusion of Functionality from Untrusted Control Sphere]
      https://scout.docker.com/v/CVE-2026-6357
      Affected range : <26.1                                                           
      Fixed version  : 26.1                                                            
      CVSS Score     : 5.3                                                             
      CVSS Vector    : CVSS:4.0/AV:L/AC:L/AT:P/PR:H/UI:A/VC:H/VI:H/VA:N/SC:N/SI:N/SA:N 
    
    ✗ MEDIUM CVE-2026-3219 [Unrestricted Upload of File with Dangerous Type]
      https://scout.docker.com/v/CVE-2026-3219
      Affected range : <=26.0.1                                                        
      Fixed version  : not fixed                                                       
      CVSS Score     : 4.6                                                             
      CVSS Vector    : CVSS:4.0/AV:L/AC:L/AT:N/PR:N/UI:A/VC:N/VI:L/VA:N/SC:N/SI:N/SA:N 
    
    ✗ LOW CVE-2026-1703 [Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')]
      https://scout.docker.com/v/CVE-2026-1703
      Affected range : <26.0                                                           
      Fixed version  : 26.0                                                            
      CVSS Score     : 2.0                                                             
      CVSS Vector    : CVSS:4.0/AV:N/AC:L/AT:P/PR:L/UI:A/VC:N/VI:L/VA:N/SC:N/SI:N/SA:N 
    

   0C     0H     1M     0L  busybox 1.37.0-r30
pkg:apk/alpine/busybox@1.37.0-r30?os_name=alpine&os_version=3.23

    ✗ MEDIUM CVE-2025-60876
      https://scout.docker.com/v/CVE-2025-60876
      Affected range : <=1.37.0-r30 
      Fixed version  : not fixed    
    

   0C     0H     0M     1L  xz 5.8.2-r0
pkg:apk/alpine/xz@5.8.2-r0?os_name=alpine&os_version=3.23

    ✗ LOW CVE-2026-34743
      https://scout.docker.com/v/CVE-2026-34743
      Affected range : <5.8.3-r0 
      Fixed version  : 5.8.3-r0  
    


7 vulnerabilities found in 4 packages
  CRITICAL  0 
  HIGH      1 
  MEDIUM    4 
  LOW       2 

```

# Uzasadnienie zagrożeń

W obrazie wykryto jedno zagrożenie HIGH (CVE-2026-24049) w pakiecie wheel dotyczącego ograniczeń ścieżki dostępu (Path Traversal). Pakiet wheel jest narzędziem wykorzystywanym tylko podczas budowania obrazu, a nie w trakcie działania aplikacji, dlatego to zagrożenie/podatność nie zagraża bezpieczeństwu w środowisku docelowym kontenera, ponieważ biblioteka ta nie jest wywoływana podczas pracy serwera aplikacji.


## Konfiguracja środowiska

Utworzenie i uruchomienie buildera

```bash
docker buildx create --name mybuilder --driver docker-container --use --bootstrap
```

```bash
[+] Building 3.3s (1/1) FINISHED                                            
 => [internal] booting buildkit                                        3.3s
 => => pulling image moby/buildkit:buildx-stable-1                     2.2s
 => => creating container buildx_buildkit_mybuilder0                   1.2s
mybuilder
```

Tożsamość SSH

```bash
eval $(ssh-agent -s)
ssh-add ~/.ssh/ghklucz3003_id_ed25519
```

Budowa wieloplatformowa

```bash
docker buildx build \
  --no-cache \
  --platform linux/amd64,linux/arm64 \
  -t mrcellar/lab_pogoda:latest \
  --cache-to type=registry,ref=mrcellar/lab_pogoda:cache,mode=max \
  --cache-from type=registry,ref=mrcellar/lab_pogoda:cache \
  --ssh default \
  --push .

```

```bash
[+] Building 100.2s (33/33) FINISHED             docker-container:mybuilder
 => [internal] load build definition from Dockerfile                   0.0s
 => => transferring dockerfile: 1.10kB                                 0.0s
 => resolve image config for docker-image://docker.io/docker/dockerfi  0.9s
 => CACHED docker-image://docker.io/docker/dockerfile:1@sha256:2780b5  0.0s
 => => resolve docker.io/docker/dockerfile:1@sha256:2780b5c3bab67f1f7  0.0s
 => [linux/arm64 internal] load metadata for docker.io/library/python  0.3s
 => [linux/amd64 internal] load metadata for docker.io/library/python  0.5s
 => [internal] load .dockerignore                                      0.0s
 => => transferring context: 671B                                      0.0s
 => ERROR importing cache manifest from mrcellar/lab_pogoda:cache      0.6s
 => [internal] load build context                                      0.0s
 => => transferring context: 37B                                       0.0s
 => CACHED [linux/arm64 builder 1/7] FROM docker.io/library/python:3.  0.1s
 => => resolve docker.io/library/python:3.11-alpine@sha256:8b5bfdb1fd  0.0s
 => CACHED [linux/amd64 builder 1/7] FROM docker.io/library/python:3.  0.1s
 => => resolve docker.io/library/python:3.11-alpine@sha256:8b5bfdb1fd  0.1s
 => CACHED [linux/amd64 stage-1 2/5] WORKDIR /app                      0.0s
 => CACHED [linux/arm64 stage-1 2/5] WORKDIR /app                      0.0s
 => [linux/arm64 stage-1 3/5] RUN adduser -D appuser                   0.3s
 => [linux/arm64 builder 2/7] RUN apk add --no-cache git openssh-cli  12.3s
 => [linux/amd64 stage-1 3/5] RUN adduser -D appuser                   0.2s
 => [linux/amd64 builder 2/7] RUN apk add --no-cache git openssh-clie  8.4s
 => [linux/amd64 builder 3/7] WORKDIR /app                             0.1s
 => [linux/amd64 builder 4/7] RUN mkdir -p -m 0700 ~/.ssh && ssh-keys  2.8s
 => [linux/amd64 builder 5/7] RUN --mount=type=ssh git clone git@gith  2.7s
 => [linux/arm64 builder 3/7] WORKDIR /app                             0.1s
 => [linux/arm64 builder 4/7] RUN mkdir -p -m 0700 ~/.ssh && ssh-keys  3.9s
 => [linux/amd64 builder 6/7] COPY requirements.txt .                  0.1s
 => [linux/amd64 builder 7/7] RUN pip install --user --no-cache-dir -  7.1s
 => [linux/arm64 builder 5/7] RUN --mount=type=ssh git clone git@gith  3.2s
 => [linux/arm64 builder 6/7] COPY requirements.txt .                  0.1s
 => [linux/arm64 builder 7/7] RUN pip install --user --no-cache-dir   34.5s
 => [linux/amd64 stage-1 4/5] COPY --from=builder /root/.local /home/  0.1s
 => [linux/amd64 stage-1 5/5] COPY --from=builder --chown=appuser:app  0.1s
 => [linux/arm64 stage-1 4/5] COPY --from=builder /root/.local /home/  0.1s
 => [linux/arm64 stage-1 5/5] COPY --from=builder --chown=appuser:app  0.1s
 => exporting to image                                                29.0s
 => => exporting layers                                                0.8s
 => => exporting manifest sha256:aecaf7cff851f77c3823c1ae479d986ab545  0.0s
 => => exporting config sha256:b3d619511d02771188f2d3c5a7a9324189eebe  0.0s
 => => exporting attestation manifest sha256:81bfd6eb2bb77c0e41c0ed75  0.0s
 => => exporting manifest sha256:104ba7924e3e8adea8037d33d1fece85922f  0.0s
 => => exporting config sha256:72ad2a3fceda81bcf92f03af84b1b46b29a1aa  0.0s
 => => exporting attestation manifest sha256:5ff1261a92039b8769c58c8c  0.0s
 => => exporting manifest list sha256:9f6422e8bab2e486046a363ae7a279b  0.0s
 => => pushing layers                                                 15.3s
 => => pushing manifest for docker.io/mrcellar/lab_pogoda:latest@sha  12.7s
 => exporting cache to registry                                       42.3s
 => => preparing build cache for export                                2.3s
 => => sending cache export                                           40.0s
 => => writing layer sha256:20174a8a2ef586cca92812c89f4454b70135862b  10.8s
 => => writing layer sha256:08ecfbef09da24ab26f8e598a2ee22be4d166b8f  14.0s
 => => writing layer sha256:1e6fb1ada2c00ccee137e79b13fe7c1cbcb8599d6  0.2s
 => => writing layer sha256:06dae15d667c1ac7e9add0bbb82b5f63c37c30ad  12.2s
 => => writing layer sha256:2d27834a976a180a5a91fa422f1ad444d4e03e24  21.9s
 => => writing layer sha256:3381adc6cff318bca54d18fa8aa41aa32f0a4010b  1.0s
 => => writing layer sha256:3e11d323b135543da163aae512706626d0c319020  0.6s
 => => writing layer sha256:3e77490d7efda81dfe52b629a83d8948b45ba6b31  0.5s
 => => writing layer sha256:59479614ba433e123241d4c4f74fa51f1b579da33  0.2s
 => => writing layer sha256:5c671a5c7ab3e0fe7526f4ff0a233b54727a1f407  0.2s
 => => writing layer sha256:6122644c3d8534fe3c196b1aa8e50a755ea94506  23.0s
 => => writing layer sha256:683d3ca6d58b9e99372bc8657c24a912122512729  0.3s
 => => writing layer sha256:6a0ac1617861a677b045b7ff88545213ec31c0ff0  0.3s
 => => writing layer sha256:72007735a976b0c2eb6bec4bdfc27f30012e9253b  1.4s
 => => writing layer sha256:774ed40815e3ad1ca1518c34b4de6a8aea45918e9  0.2s
 => => writing layer sha256:7bc664c1033b7cdbb5b91725db29de0c5b00e3ab  10.4s
 => => writing layer sha256:7d31d364820aadf10b5b7ef8d1b394c3bb45d665  10.1s
 => => writing layer sha256:9a7b2da3971f58416488360a0f014629b1f8f75e6  1.5s
 => => writing layer sha256:9f4ed7abe9560f1ccd72a1621846a9ebf81f23b95  1.7s
 => => writing layer sha256:a192407ea606579cb7847f6402ccd7f294d7b79de  1.3s
 => => writing layer sha256:a33cc6ba0b9ecd7802d2360a3c8e230ea5ec3abf0  1.2s
 => => writing layer sha256:a3cdbf8a2dc26c3a605561b087683a9f35f32ef1e  1.6s
 => => writing layer sha256:a74633ed29ce2ce2fb62c55125502d682b5fe9b47  0.2s
 => => writing layer sha256:c44d4dd2b11bc0b45f6c5e9cc790ef4150bfd743f  3.4s
 => => writing layer sha256:d17f077ada118cc762df373ff803592abf2dfa3dd  0.2s
 => => writing layer sha256:d3d55d52d6759d2325dfaf979130a81857b20a8e6  2.0s
 => => writing layer sha256:ea923c2ed79ed2d7163aacc54686501cb33392156  0.9s
 => => writing layer sha256:fd8e41ac72776720a5ce79d8a0c66e75ae8d3d836  0.6s
 => => writing config sha256:877b6759698ae8427b1a010fa6c2f81f6590601c  1.6s
 => => writing cache image manifest sha256:aa6d651ee74794950fe30d9706  2.7s
 => [auth] mrcellar/lab_pogoda:pull,push token for registry-1.docker.  0.0s
------
 > importing cache manifest from mrcellar/lab_pogoda:cache:
------
```

## Weryfikacja obrazu i manifestu

Komenda: 

```bash
docker buildx imagetools inspect mrcellar/lab_pogoda:latest
```

Wynik:

```bash

Name:      docker.io/mrcellar/lab_pogoda:latest
MediaType: application/vnd.oci.image.index.v1+json
Digest:    sha256:9f6422e8bab2e486046a363ae7a279b9aad2ad89beec2fe651970ad40e7ac213
           
Manifests: 
  Name:        docker.io/mrcellar/lab_pogoda:latest@sha256:aecaf7cff851f77c3823c1ae479d986ab545c7ed9ea75312c98b9355c291cd0f
  MediaType:   application/vnd.oci.image.manifest.v1+json
  Platform:    linux/amd64
               
  Name:        docker.io/mrcellar/lab_pogoda:latest@sha256:104ba7924e3e8adea8037d33d1fece85922fd605d7a83f4d663c6eed3d7857dc
  MediaType:   application/vnd.oci.image.manifest.v1+json
  Platform:    linux/arm64
               
  Name:        docker.io/mrcellar/lab_pogoda:latest@sha256:81bfd6eb2bb77c0e41c0ed7587cd122d2719c3049697136abaf394249c7e4d1d
  MediaType:   application/vnd.oci.image.manifest.v1+json
  Platform:    unknown/unknown
  Annotations: 
    vnd.docker.reference.type:   attestation-manifest
    vnd.docker.reference.digest: sha256:aecaf7cff851f77c3823c1ae479d986ab545c7ed9ea75312c98b9355c291cd0f
               
  Name:        docker.io/mrcellar/lab_pogoda:latest@sha256:5ff1261a92039b8769c58c8c580ea4a314f3e9d1b28b4146b399b00332341756
  MediaType:   application/vnd.oci.image.manifest.v1+json
  Platform:    unknown/unknown
  Annotations: 
    vnd.docker.reference.digest: sha256:104ba7924e3e8adea8037d33d1fece85922fd605d7a83f4d663c6eed3d7857dc
    vnd.docker.reference.type:   attestation-manifest
```