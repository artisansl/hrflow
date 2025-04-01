# Include values from .env file
include .env

# ================================================================================
# HELPERS
# ================================================================================

## help: print this help message
.PHONY: help
help:
	@echo 'Usage:'
	@sed -n 's/^##//p' ${MAKEFILE_LIST} | column -t -s ':' | sed -e 's/^/ /'

# Confirmation target
.PHONY: confirm
confirm:
	@echo -n 'Are you sure? [y/N]' && read ans && [ $${ans:-N} = y ]



# ================================================================================
# DEVELOPMENT
# ================================================================================

# Live Reload
# run/api: run the ./cmd/api/ application
dev:
	@if command -v air > /dev/null; then \
            air; \
            echo "Watching...";\
        else \
            read -p "Go's 'air' is not installed on your machine. Do you want to install it? [Y/n] " choice; \
            if [ "$$choice" != "n" ] && [ "$$choice" != "N" ]; then \
                go install github.com/air-verse/air@latest; \
                air; \
                echo "Watching...";\
            else \
                echo "You chose not to install air. Exiting..."; \
                exit 1; \
            fi; \
        fi



## run/api: run the ./cmd/api/ application
.PHONY: run/api
run/api:
	go run ./cmd/api -db-dsn=${DATABASE_URL}

## db/sqlite: connect to database using sqlite
.PHONY: db/sqlite
db/sqlite:
	sqlite ${DATABASE_URL}

## db/migrations/new name=$1: creates a new database migration
.PHONY: db/migrations/new
db/migrations/new:
	@echo 'Creating migration files for ${name}'
	goose -dir=migrations create ${name} sql

## db/migrations/up: apply all up database migrations
.PHONY: db/migrations/up
db/migrations/up: confirm
	@echo 'Running up migrations...'
	goose -dir=migrations postgres ${DATABASE_URL} up

## db/migrations/down: apply all down database migrations
.PHONY: db/migrations/down
db/migrations/down:
	@echo 'Running down migrations...'
	goose -dir=migrations postgres ${DATABASE_URL} down



# ================================================================================
# QUALITY CONTROL
# ================================================================================

## audit: tidy dependencies and format, vet and test all code
.PHONY: audit
audit:
	@echo 'Tidying and verifying module dependencies...'
	go mod tidy
	go mod verify

	@echo 'Formatting code...'
	go fmt ./...
	gofmt -s -w .

	@echo 'Vetting code...'
	go vet ./...

	@echo 'Running tests...'
	go test ./... -count=1



# ================================================================================
# BUILD
# ================================================================================

## build/api: build the cmd/api application
.PHONY: build/api
build/api:
	@echo 'Building cmd/api...'
	go build -ldflags="-s" -o=./bin/api ./cmd/api
	@echo 'Building for linux...'
	GOOS=linux GOARCH=amd64 go build -ldflags="-s" -o=./bin/api_linux_amd64 ./cmd/api
	@echo 'Building for OSX/darwin...'
	GOOS=darwin GOARCH=amd64 go build -ldflags="-s" -o=./bin/api_darwin_amd64 ./cmd/api
	@echo 'Building for windows...'
	GOOS=windows GOARCH=amd64 go build -ldflags="-s" -o=./bin/api_windows_amd64 ./cmd/api



# ================================================================================
# CLEAN
# ================================================================================

## clean: clean up files
.PHONY: clean
clean:
	@echo 'Cleaning up files...'
	rm -rvf ./bin/*
