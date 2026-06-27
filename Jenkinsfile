pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'ls -la'
                sh 'echo "Workspace: $WORKSPACE"'
            }
        }

        stage('Sprawdzenie skladni Pythona') {
            steps {
                sh '''
                    echo "=== Rozpoczynam sprawdzanie plikow Python ==="
                    BLEDY=0

                    for plik in *.py; do
                        echo ""
                        echo "--- Plik: $plik ---"

                        if docker run --rm \
                            -v jenkins_home:/var/jenkins_home \
                            -w "$WORKSPACE" \
                            python:3.11-slim \
                            python -m py_compile "$plik"
                        then
                            echo ">>> $plik: kod POPRAWNY"
                        else
                            echo ">>> $plik: kod NIEPOPRAWNY (blad skladni)"
                            BLEDY=$((BLEDY + 1))
                        fi
                    done

                    echo ""
                    echo "=== Podsumowanie ==="
                    echo "Plikow z bledami: $BLEDY"

                    if [ $BLEDY -gt 0 ]; then
                        echo "Build oznaczony jako FAILED - znaleziono bledy"
                        exit 1
                    else
                        echo "Wszystkie pliki poprawne"
                    fi
                '''
            }
        }
    }
}