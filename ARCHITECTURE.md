# Shopizer Admin - Architecture Diagram

## High-Level Architecture

```mermaid
graph TB
    subgraph Browser["Browser (Angular SPA)"]
        subgraph AppModule["AppModule"]
            Router["Angular Router\n(Hash-based)"]
            AuthInterceptor["AuthInterceptor\n(JWT injection)"]
            ErrorInterceptor["GlobalErrorInterceptor\n(Error handling)"]
            I18n["ngx-translate\n(i18n)"]
        end

        subgraph CoreModule["@core Module"]
            MockData["Mock Data Services"]
            Utils["Utilities\n(Layout, State)"]
        end

        subgraph ThemeModule["@theme Module"]
            Header["Header Component"]
            Footer["Footer Component"]
            Layout["Sample Layout"]
            Pipes["Pipes\n(capitalize, plural, round)"]
            ImageBrowser["Image Browser"]
        end

        subgraph PagesModule["Pages Module (Lazy Loaded, AuthGuard)"]
            Home["Home\n/pages/home"]

            subgraph Auth["Auth Module\n/auth"]
                Login["Login"]
                Register["Register"]
                ForgotPwd["Forgot Password"]
                ResetPwd["Reset Password"]
            end

            subgraph UserMgmt["User Management\n/pages/user-management"]
                UserProfile["My Profile"]
                UserList["Users List"]
                CreateUser["Create User"]
                ChangePassword["Change Password"]
            end

            subgraph StoreMgmt["Store Management\n/pages/store-management"]
                StoreDetails["Store Details"]
                StoresList["Stores List"]
                CreateStore["Create Store"]
                StoreBranding["Store Branding"]
                RetailerList["Retailer List"]
            end

            subgraph Catalogue["Catalogue Module\n/pages/catalogue"]
                Products["Products"]
                Categories["Categories"]
                Brands["Brands"]
                Options["Options & Values"]
                Variations["Variations"]
                ProductTypes["Product Types"]
                ProductGroups["Product Groups"]
                Catalogues["Catalogues"]
            end

            subgraph ContentMgmt["Content Management\n/pages/content"]
                ContentPages["Pages"]
                ContentBoxes["Boxes"]
                ContentImages["Images"]
                ContentFiles["Files"]
            end

            subgraph Orders["Orders\n/pages/orders"]
                OrderList["Order List"]
                OrderDetails["Order Details"]
                OrderHistory["Order History"]
                OrderInvoice["Invoice"]
            end

            subgraph Customers["Customers\n/pages/customer"]
                CustomerList["Customer List"]
                CustomerDetails["Customer Details"]
            end

            subgraph Shipping["Shipping\n/pages/shipping"]
                ShipConfig["Expedition Config"]
                ShipMethods["Shipping Methods"]
                ShipOrigin["Origin"]
                ShipPackaging["Packaging"]
            end

            subgraph Payment["Payment\n/pages/payment"]
                PaymentMethods["Payment Methods"]
                PaymentConfig["Configure Module"]
            end

            subgraph TaxMgmt["Tax Management\n/pages/tax-management"]
                TaxClasses["Tax Classes"]
                TaxRates["Tax Rates"]
            end
        end

        subgraph SharedModule["Shared Module"]
            AuthGuard["AuthGuard"]
            CrudService["CrudService\n(HTTP wrapper)"]
            ConfigService["ConfigService"]
            UserService["UserService"]
            StorageService["StorageService\n(localStorage)"]
            SecurityService["SecurityService"]
            TokenService["TokenService"]
            AuthService["AuthService"]
        end
    end

    subgraph Backend["Backend Services"]
        MainAPI["Main API\nlocalhost:8080/api"]
        ShippingAPI["Shipping API\nlocalhost:9090/shipping/api/v1"]
    end

    Router -->|"canActivate"| AuthGuard
    AuthGuard -->|"checks token"| TokenService
    AuthInterceptor -->|"injects Bearer token"| CrudService
    CrudService -->|"HTTP requests"| MainAPI
    CrudService -->|"HTTP requests"| ShippingAPI
```

---

## Module Dependency Graph

```mermaid
graph LR
    AppModule --> CoreModule
    AppModule --> ThemeModule
    AppModule --> AppRoutingModule
    AppModule --> SharedModule

    AppRoutingModule -->|lazy| AuthModule
    AppRoutingModule -->|lazy| PagesModule

    PagesModule -->|lazy| HomeModule
    PagesModule -->|lazy| UserManagementModule
    PagesModule -->|lazy| StoreManagementModule
    PagesModule -->|lazy| CatalogueModule
    PagesModule -->|lazy| ContentModule
    PagesModule -->|lazy| OrdersModule
    PagesModule -->|lazy| CustomersModule
    PagesModule -->|lazy| ShippingModule
    PagesModule -->|lazy| PaymentModule
    PagesModule -->|lazy| TaxManagementModule

    CatalogueModule --> SharedModule
    OrdersModule --> SharedModule
    CustomersModule --> SharedModule
    ShippingModule --> SharedModule
    StoreManagementModule --> SharedModule
    ContentModule --> SharedModule
    UserManagementModule --> SharedModule
    PaymentModule --> SharedModule
    TaxManagementModule --> SharedModule
```

---

## Authentication Flow

```mermaid
sequenceDiagram
    participant User
    participant Angular
    participant AuthGuard
    participant AuthInterceptor
    participant TokenService
    participant API

    User->>Angular: Navigate to /pages/*
    Angular->>AuthGuard: canActivate()
    AuthGuard->>TokenService: getToken()
    alt No token
        AuthGuard->>Angular: redirect to /auth
        User->>Angular: Submit login form
        Angular->>API: POST /v1/private/login
        API-->>Angular: JWT token
        Angular->>TokenService: saveToken(jwt)
        Angular->>Angular: redirect to /pages
    else Has token
        AuthGuard-->>Angular: true (allow)
        Angular->>AuthInterceptor: HTTP request
        AuthInterceptor->>TokenService: getToken()
        AuthInterceptor->>API: Request + Authorization: Bearer <token>
        API-->>Angular: Response
    end
```

---

## Data Flow

```mermaid
flowchart LR
    Component -->|calls| FeatureService
    FeatureService -->|calls| CrudService
    CrudService -->|HTTP + JWT| BackendAPI

    BackendAPI -->|JSON response| CrudService
    CrudService -->|Observable| FeatureService
    FeatureService -->|Observable| Component
    Component -->|renders| Template
```

---

## Role-Based Access Control

```mermaid
graph TD
    Login["Login"] --> RoleCheck{"Role?"}

    RoleCheck -->|superadmin| SA["Full Access\n- All stores\n- All features\n- Marketplace mode"]
    RoleCheck -->|admin| AD["Admin Access\n- Store management\n- Users\n- Catalogue\n- Orders"]
    RoleCheck -->|adminretail| AR["Retail Admin\n- Own store\n- Catalogue\n- Orders"]
    RoleCheck -->|admincatalogue| AC["Catalogue Admin\n- Products\n- Categories\n- Options"]
    RoleCheck -->|adminorder| AO["Order Admin\n- Orders only"]
    RoleCheck -->|admincontent| ACO["Content Admin\n- Pages\n- Boxes\n- Images"]
    RoleCheck -->|adminstore| AS["Store Admin\n- Store settings"]
```

---

## Store Modes

```mermaid
graph LR
    subgraph STANDARD["STANDARD Mode"]
        S1["Categories: per store"]
        S2["Options: per store"]
        S3["Multi-store support"]
    end

    subgraph MARKETPLACE["MARKETPLACE Mode"]
        M1["Categories: global\n(superadmin only)"]
        M2["Options: global"]
        M3["Retailer stores"]
    end

    subgraph BTB["BTB Mode"]
        B1["Business-to-Business"]
    end

    EnvConfig["environment.mode"] --> STANDARD
    EnvConfig --> MARKETPLACE
    EnvConfig --> BTB
```
