node {
  withEnv([
    "PATH=./temp/bin:$PATH",
    "APP_NAME=PKI-Sign-demo-project",
    "APP_VERSION=1.1.3",
    "AUTHOR_NAME=John-Smith", 
    "AUTHOR_EMAIL=jhon@thiscompany.com",
    "AUTHOR_PHONE=555-8426157",
    "SUPPLIER_NAME=Scribe-Security",
    "SUPPLIER_URL=www.scribesecurity.com",
    "SUPPLIER_EMAIL=info@scribesecurity.com",
    "SUPPLIER_PHONE=001-001-0011",
    "PRIVATE_KEY=xxx",
    "SIGNING_CERT=yyy",
    "CA_CERT=xxx",
    "DOCKERHUB_USERNAME: scribesecurity"
   
  ]) 
   {
    stage('install') {
      cleanWs()
      sh 'curl -sSfL https://get.scribesecurity.com/install.sh | sh -s -- -b ./temp/bin'
    }
    
    stage('checkout') {
withCredentials([
        usernamePassword(credentialsId: 'Dockerhub_pat', passwordVariable: 'DOCKERHUB_PAT') ])
            sh 'git clone -b main --single-branch https://github.com/scribe-security/jenkins-pki-example.git'
            sh 'cd jenkins-pki-example; docker build -t pki-test:latest -f ./orig-Dockerfile .'
            sh 'cat $DOCKERHUB_PAT'
     }
    
    stage('git-commit-sbom') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ]) 
      
      {
        sh '''
          PRIVATE_KEY=$(cat $KEY_FILE)
          SIGNING_CERT=$(cat $SIG_CERT_FILE)     
          CA_CERT=$(cat $CA_CERT_FILE)
          valint bom git:jenkins-pki-example/. \
            --components packages,files,dep \
            --context-type jenkins \
            --format attest\
            --attest.default x509 \
            --ca $CA_CERT_FILE \
            --cert $SIG_CERT_FILE \
            --key $KEY_FILE \
            --output-directory ./scribe/valint \
            -E -P $SCRIBE_CLIENT_SECRET \
            --product-key $APP_NAME \
            --author-name $AUTHOR_NAME --author-email AUTHOR_EMAIL --author-phone $AUTHOR_PHONE  \
            --supplier-name $SUPPLIER_NAME --supplier-url $SUPPLIER_URL --supplier-email $SUPPLIER_EMAIL  \
            --supplier-phone $SUPPLIER_PHONE \
            --label is_git_commit \
            -f '''
      }
    }
     stage('bom-git') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ]) 
      
      {
          sh '''
          
          valint bom git:jenkins-pki-example/. \
            --components packages,files,dep \
            --context-type jenkins \
            --format attest\
            --attest.default x509 \
            --ca $CA_CERT_FILE \
            --cert $SIG_CERT_FILE \
            --key $KEY_FILE \
            --output-directory ./scribe/valint \
            -E -P $SCRIBE_CLIENT_SECRET \
            --product-key $APP_NAME \
            --author-name $AUTHOR_NAME --author-email AUTHOR_EMAIL --author-phone $AUTHOR_PHONE  \
            --supplier-name $SUPPLIER_NAME --supplier-url $SUPPLIER_URL --supplier-email $SUPPLIER_EMAIL  \
            --supplier-phone $SUPPLIER_PHONE \
            -f '''
            
      }
    }

 /*   stage('provenance-image') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ])   
      {
      sh '''
          valint slsa pki-test:latest \
            --components packages,files,dep \
            --context-type jenkins \
            --format attest\
            --attest.default x509 \
            --ca $CA_CERT_FILE \
            --cert $SIG_CERT_FILE \
            --key $KEY_FILE \
            --output-directory ./scribe/valint \
            -E -P $SCRIBE_CLIENT_SECRET \
            --product-key $APP_NAME \
            --author-name $AUTHOR_NAME --author-email AUTHOR_EMAIL --author-phone $AUTHOR_PHONE  \
            --supplier-name $SUPPLIER_NAME --supplier-url $SUPPLIER_URL --supplier-email $SUPPLIER_EMAIL  \
            --supplier-phone $SUPPLIER_PHONE \
            -f '''
          
  
      }
    } */

     stage('bom-image') {
      withCredentials([
        usernamePassword(credentialsId: 'scribe-production-auth-id', usernameVariable: 'SCRIBE_CLIENT_ID', passwordVariable: 'SCRIBE_CLIENT_SECRET'),
        file(credentialsId: 'key-file', variable: 'KEY_FILE'),
        file(credentialsId: 'sig-cert-file', variable: 'SIG_CERT_FILE'),
        file(credentialsId: 'ca-cert-file', variable: 'CA_CERT_FILE')
      ])   
      {
      sh '''
          valint bom pki-test:latest \
            --components packages,files,dep \
            --context-type jenkins \
            --format attest\
            --attest.default x509 \
            --ca $CA_CERT_FILE \
            --cert $SIG_CERT_FILE \
            --key $KEY_FILE \
            --output-directory ./scribe/valint \
            -E -P $SCRIBE_CLIENT_SECRET \
            --product-key $APP_NAME \
            --author-name $AUTHOR_NAME --author-email AUTHOR_EMAIL --author-phone $AUTHOR_PHONE  \
            --supplier-name $SUPPLIER_NAME --supplier-url $SUPPLIER_URL --supplier-email $SUPPLIER_EMAIL  \
            --supplier-phone $SUPPLIER_PHONE \
            -f '''
      }
    }

    
     
  }
}
