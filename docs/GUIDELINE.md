## Deployment

## Integration

### Administrator

In this guideline I am going to use the domain `https://kratos.svc.cluster.local` as the host of admin API, you NEED TO change it after configure the deployment. The host must be setup with SSL certificate to secure your API

The Administrator APIs don't come with integrated access control. This means that all requests sent to their APIs are considered authenticated, authorized, and will be executed. So you should only expose those APIs to trust servers or netwokrs. The best practise is using the Administrator APIs inside a private network with server-server comminucation

#### Create new account

Options:

- `traits.email`: the email of new account
- `credentials.password.config.password` the password of new account

Request:

```bash
curl --location 'https://kratos.svc.cluster.local/admin/identities' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header 'Authorization;' \
--data-raw '{
    "schema_id": "default",
    "traits": {
        "email": "tuan.nguyen930708@gmail.com"
    },
    "credentials": {
        "password": {
            "config": {
                "password": "thepassword_thatis_STRONG"
            }
        }
    }
}
'
```

Response Body:

```json
{
    "id": "7ca3df0e-874b-44f7-a258-6223d49f5952",
    "credentials": {
        "password": {
            "type": "password",
            "identifiers": [
                "tuan.nguyen930708@gmail.com"
            ],
            "version": 0,
            "created_at": "0001-01-01T00:00:00Z",
            "updated_at": "0001-01-01T00:00:00Z"
        }
    },
    "schema_id": "default",
    "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
    "state": "active",
    "state_changed_at": "2023-12-20T07:20:43.511500574Z",
    "traits": {
        "email": "tuan.nguyen930708@gmail.com"
    },
    "verifiable_addresses": [
        {
            "id": "688c6fc6-0d75-49bb-93ed-0860d1c600c0",
            "value": "tuan.nguyen930708@gmail.com",
            "verified": false,
            "via": "email",
            "status": "pending",
            "created_at": "2023-12-20T07:20:43.513698Z",
            "updated_at": "2023-12-20T07:20:43.513698Z"
        }
    ],
    "recovery_addresses": [
        {
            "id": "c71a5f75-7865-47ce-93c4-28bd3248fada",
            "value": "tuan.nguyen930708@gmail.com",
            "via": "email",
            "created_at": "2023-12-20T07:20:43.514484Z",
            "updated_at": "2023-12-20T07:20:43.514484Z"
        }
    ],
    "metadata_public": null,
    "created_at": "2023-12-20T07:20:43.512549Z",
    "updated_at": "2023-12-20T07:20:43.512549Z",
    "organization_id": null
}
```

#### Get an account

Options

- include_credential
    - `password`: return the password credentials (with the `hashed_password` attributes)
    - `oidc`: return the initial OAuth 2.0 Access Token, OAuth 2.0 Refresh Token and the OpenID Connect ID Token if available.

Request

```bash
curl --location https://kratos.svc.cluster.local/admin/identities/7ca3df0e-874b-44f7-a258-6223d49f5952?include_credential=password&include_credential=oidc' \
--header 'Accept: application/json'
```

Response Body

```json
{
    "id": "7ca3df0e-874b-44f7-a258-6223d49f5952",
    "credentials": {
        "password": {
            "type": "password",
            "identifiers": [
                "tuan.nguyen930708@gmail.com"
            ],
            "config": {
                "hashed_password": "$2a$12$/dkPt64Ngazx/acXfp8tTOwCRYGNThCm9rZNyrt1a2Ygtjksatgre"
            },
            "version": 0,
            "created_at": "2023-12-20T07:20:43.515658Z",
            "updated_at": "2023-12-20T07:20:43.515658Z"
        }
    },
    "schema_id": "default",
    "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
    "state": "active",
    "state_changed_at": "2023-12-20T07:20:43.5115Z",
    "traits": {
        "email": "tuan.nguyen930708@gmail.com"
    },
    "verifiable_addresses": [
        {
            "id": "688c6fc6-0d75-49bb-93ed-0860d1c600c0",
            "value": "tuan.nguyen930708@gmail.com",
            "verified": false,
            "via": "email",
            "status": "pending",
            "created_at": "2023-12-20T07:20:43.513698Z",
            "updated_at": "2023-12-20T07:20:43.513698Z"
        }
    ],
    "recovery_addresses": [
        {
            "id": "c71a5f75-7865-47ce-93c4-28bd3248fada",
            "value": "tuan.nguyen930708@gmail.com",
            "via": "email",
            "created_at": "2023-12-20T07:20:43.514484Z",
            "updated_at": "2023-12-20T07:20:43.514484Z"
        }
    ],
    "metadata_public": null,
    "metadata_admin": null,
    "created_at": "2023-12-20T07:20:43.512549Z",
    "updated_at": "2023-12-20T07:20:43.512549Z",
    "organization_id": null
}
```

#### Update an account

There are various values you can add or replace, there are some usecase that may useful for you

##### Mark an email as verified manually

Request

```bash
curl --location --request PATCH https://kratos.svc.cluster.local/admin/identities/7ca3df0e-874b-44f7-a258-6223d49f5952' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--data '[
    {
        "op": "replace",
        "path": "/verifiable_addresses/0/verified",
        "value": true
    },
    {
        "op": "replace",
        "path": "/verifiable_addresses/0/status",
        "value": "completed"
    }
]'
```

Response Body

```json
{
    "id": "7ca3df0e-874b-44f7-a258-6223d49f5952",
    "credentials": {
        "password": {
            "type": "password",
            "identifiers": [
                "tuan.nguyen930708@gmail.com"
            ],
            "version": 0,
            "created_at": "2023-12-20T07:20:43.515658Z",
            "updated_at": "2023-12-20T07:20:43.515658Z"
        }
    },
    "schema_id": "default",
    "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
    "state": "active",
    "state_changed_at": "2023-12-20T07:20:43.5115Z",
    "traits": {
        "email": "tuan.nguyen930708@gmail.com"
    },
    "verifiable_addresses": [
        {
            "id": "688c6fc6-0d75-49bb-93ed-0860d1c600c0",
            "value": "tuan.nguyen930708@gmail.com",
            "verified": true,
            "via": "email",
            "status": "completed",
            "verified_at": "2023-12-20T07:37:51.488354267Z",
            "created_at": "2023-12-20T07:20:43.513698Z",
            "updated_at": "2023-12-20T07:37:51.490121Z"
        }
    ],
    "recovery_addresses": [
        {
            "id": "c71a5f75-7865-47ce-93c4-28bd3248fada",
            "value": "tuan.nguyen930708@gmail.com",
            "via": "email",
            "created_at": "2023-12-20T07:20:43.514484Z",
            "updated_at": "2023-12-20T07:20:43.514484Z"
        }
    ],
    "metadata_public": null,
    "metadata_admin": null,
    "created_at": "2023-12-20T07:20:43.512549Z",
    "updated_at": "2023-12-20T07:20:43.512549Z",
    "organization_id": null
}
```

##### Add some propeties to metadata_public (or metadata_admin)

Request

```bash
curl --location --request PATCH https://kratos.svc.cluster.local/admin/identities/7ca3df0e-874b-44f7-a258-6223d49f5952' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header 'Authorization;' \
--data '[
  {
    "op": "add",
    "path": "/metadata_public/everyone",
    "value": {
        "can": "read",
        "cannot": "modify"
    }
  },
  {
    "op": "add",
    "path": "/metadata_public/admin",
    "value": "can_do_whatever_admin_want_to"
  },
  {
    "op": "add",
    "path": "/metadata_admin/everyone",
    "value": {
        "cannot": "read_or_modify"
    }
  },
  {
    "op": "add",
    "path": "/metadata_admin/admin",
    "value": "can_do_whatever_admin_want_to"
  }
]'
```

Response Body

```json
{
    "id": "7ca3df0e-874b-44f7-a258-6223d49f5952",
    "credentials": {
        "password": {
            "type": "password",
            "identifiers": [
                "tuan.nguyen930708@gmail.com"
            ],
            "version": 0,
            "created_at": "2023-12-20T07:20:43.515658Z",
            "updated_at": "2023-12-20T07:37:51.491144Z"
        }
    },
    "schema_id": "default",
    "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
    "state": "active",
    "state_changed_at": "2023-12-20T07:20:43.5115Z",
    "traits": {
        "email": "tuan.nguyen930708@gmail.com"
    },
    "verifiable_addresses": [
        {
            "id": "688c6fc6-0d75-49bb-93ed-0860d1c600c0",
            "value": "tuan.nguyen930708@gmail.com",
            "verified": true,
            "via": "email",
            "status": "pending",
            "verified_at": "2023-12-20T07:37:51.488354Z",
            "created_at": "2023-12-20T07:20:43.513698Z",
            "updated_at": "2023-12-20T07:37:51.490121Z"
        }
    ],
    "recovery_addresses": [
        {
            "id": "c71a5f75-7865-47ce-93c4-28bd3248fada",
            "value": "tuan.nguyen930708@gmail.com",
            "via": "email",
            "created_at": "2023-12-20T07:20:43.514484Z",
            "updated_at": "2023-12-20T07:20:43.514484Z"
        }
    ],
    "metadata_public": {
        "everyone": {
            "can": "read",
            "cannot": "modify"
        },
        "admin": "can_do_whatever_admin_want_to"
    },
    "metadata_admin": {
        "everyone": {
            "cannot": "read_or_modify"
        },
        "admin": "can_do_whatever_admin_want_to"
    },
    "created_at": "2023-12-20T07:20:43.512549Z",
    "updated_at": "2023-12-20T07:20:43.512549Z",
    "organization_id": null
}
```


#### List accounts

Options

- `per_page`: default is `250`
- `page`: default is `0`
- `credentials_identifier`: an email of the searching account if you want to find the account

Request

```bash
curl --location https://kratos.svc.cluster.local/admin/identities?per_page=250&page=0&credentials_identifier=tuan.nguyen930708%40gmail.com' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header 'Authorization;'
```

Response Body

```json
[
    {
        "id": "7ca3df0e-874b-44f7-a258-6223d49f5952",
        "credentials": {
            "password": {
                "type": "password",
                "identifiers": [
                    "tuan.nguyen930708@gmail.com"
                ],
                "version": 0,
                "created_at": "2023-12-20T07:20:43.515658Z",
                "updated_at": "2023-12-20T07:50:39.846748Z"
            }
        },
        "schema_id": "default",
        "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
        "state": "active",
        "state_changed_at": "2023-12-20T07:20:43.5115Z",
        "traits": {
            "email": "tuan.nguyen930708@gmail.com"
        },
        "verifiable_addresses": [
            {
                "id": "688c6fc6-0d75-49bb-93ed-0860d1c600c0",
                "value": "tuan.nguyen930708@gmail.com",
                "verified": true,
                "via": "email",
                "status": "completed",
                "verified_at": "2023-12-20T07:37:51.488354Z",
                "created_at": "2023-12-20T07:20:43.513698Z",
                "updated_at": "2023-12-20T07:50:39.845691Z"
            }
        ],
        "recovery_addresses": [
            {
                "id": "c71a5f75-7865-47ce-93c4-28bd3248fada",
                "value": "tuan.nguyen930708@gmail.com",
                "via": "email",
                "created_at": "2023-12-20T07:20:43.514484Z",
                "updated_at": "2023-12-20T07:20:43.514484Z"
            }
        ],
        "metadata_public": {
            "admin": "can_do_whatever_admin_want_to",
            "everyone": {
                "can": "read",
                "cannot": "modify"
            }
        },
        "metadata_admin": {
            "admin": "can_do_whatever_admin_want_to",
            "everyone": {
                "cannot": "read_or_modify"
            }
        },
        "created_at": "2023-12-20T07:20:43.512549Z",
        "updated_at": "2023-12-20T07:20:43.512549Z",
        "organization_id": null
    }
]
```

#### Delete an accounts

Request

```bash
curl --location --request DELETE https://kratos.svc.cluster.local/admin/identities/19fd3f6c-34fe-4594-a18b-fbd56fdcef5e' \
--header 'Accept: application/json' \
--header 'Authorization;'
```

### Frontend (Browser)

In this guideline I am going to use the domain `https://kratos.scrapnode.com` as the host of admin API, you NEED TO change it after configure the deployment. The host must be setup with SSL certificate to secure your API

**IMPORANT NOTE**: The browser API is heavily depended on cookkie-session model, so if you are wokring on Single Page Application, you need to send the `Cookie` header with each request you made to the Kratos Identity Server

#### Registration

1. Init the registration flow to get the flow information

    Request

    ```bash
    curl --location 'https://kratos.scrapnode.com/self-service/registration/browser' \
    --header 'Content-Type: application/json' \
    --header 'Accept: application/json' \
    ```

    Response Body

    ```json
    {
        "id": "eb2bfb29-7fe9-46ed-8785-539d990ed3ff",
        "type": "browser",
        "expires_at": "2023-12-20T09:51:01.895820353Z",
        "issued_at": "2023-12-20T09:41:01.895820353Z",
        "request_url": "https://kratos.scrapnode.com/self-service/registration/browser",
        "ui": {
            "action": "https://kratos.scrapnode.com/self-service/registration?flow=eb2bfb29-7fe9-46ed-8785-539d990ed3ff",
            "method": "POST",
            "nodes": [
                {
                    "type": "input",
                    "group": "default",
                    "attributes": {
                        "name": "csrf_token",
                        "type": "hidden",
                        "value": "tmqMMK+4Ii9vIBzA9Xr5DzajAIaIXoyGKecGRbVQDde3cWugSjH+m2bXEwwAOP6gaY3HMYr5Fg2Th364o9ZCFA==",
                        "required": true,
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {}
                },
                {
                    "type": "input",
                    "group": "password",
                    "attributes": {
                        "name": "traits.email",
                        "type": "email",
                        "required": true,
                        "autocomplete": "email",
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {
                        "label": {
                            "id": 1070002,
                            "text": "E-Mail",
                            "type": "info",
                            "context": {
                                "title": "E-Mail"
                            }
                        }
                    }
                },
                {
                    "type": "input",
                    "group": "password",
                    "attributes": {
                        "name": "password",
                        "type": "password",
                        "required": true,
                        "autocomplete": "new-password",
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {
                        "label": {
                            "id": 1070001,
                            "text": "Password",
                            "type": "info"
                        }
                    }
                },
                {
                    "type": "input",
                    "group": "password",
                    "attributes": {
                        "name": "method",
                        "type": "submit",
                        "value": "password",
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {
                        "label": {
                            "id": 1040001,
                            "text": "Sign up",
                            "type": "info"
                        }
                    }
                }
            ]
        },
        "organization_id": null,
        "state": "choose_method"
    }
    ```
    
    Response Header:
    
    ```
    set-cookie: csrf_token_6c0cb2d8e17be2aeca168648961b34f465bed2ffa37dd7fa2ad8b4867a3b1f15=ARvnkOWJ3LQJ9w/M9UIHr18ux7cCp5qLumB4/RaGT8M=; Path=/; Domain=scrapnode.com; Max-Age=31536000; HttpOnly; Secure; SameSite=Lax
    ```

2. Submit the registration flow with both information you obtained from the step #1 and user input

    Request

    - Header `Cookie`: obtain from the `set-cookie` header in the response header in step #1
    - Query `flow`: obtain from the `id` attribute in the response body in step #1
    - Body `traits.email`: the account email
    - Body `password`: the account password
    - Body `csrf_token`: the CSRF token that is obtained from the `ui.nodes[0].attributes.value` in the response body in step #1 

    ```bash
    curl --location 'https://kratos.scrapnode.com/self-service/registration?flow=eb2bfb29-7fe9-46ed-8785-539d990ed3ff' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --header 'Accept: application/json' \
    --header 'Cookie: csrf_token_6c0cb2d8e17be2aeca168648961b34f465bed2ffa37dd7fa2ad8b4867a3b1f15=2rZbiLjhNcHUB9TfVdWIHhfnIr37GPaCqCceyxGNBXo=' \
    --data-urlencode 'traits.email=gyh55799@nezid.com' \
    --data-urlencode 'password=1password' \
    --data-urlencode 'method=password' \
    --data-urlencode 'csrf_token=tmqMMK+4Ii9vIBzA9Xr5DzajAIaIXoyGKecGRbVQDde3cWugSjH+m2bXEwwAOP6gaY3HMYr5Fg2Th364o9ZCFA=='
    ```

    Response Body

    ```json
    {
        "session": {
            "id": "9db7bc79-66ab-4bfe-98e0-9f55cf761318",
            "active": true,
            "expires_at": "2023-12-21T09:42:28.836782153Z",
            "authenticated_at": "2023-12-20T09:42:28.836788985Z",
            "authenticator_assurance_level": "aal1",
            "authentication_methods": [
                {
                    "method": "password",
                    "aal": "aal1",
                    "completed_at": "2023-12-20T09:42:28.83678172Z"
                }
            ],
            "issued_at": "2023-12-20T09:42:28.836782153Z",
            "identity": {
                "id": "d95ad291-d27b-4eaa-89bb-4c151c757ae6",
                "schema_id": "default",
                "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
                "state": "active",
                "state_changed_at": "2023-12-20T09:42:28.832395601Z",
                "traits": {
                    "email": "gyh55799@nezid.com"
                },
                "verifiable_addresses": [
                    {
                        "id": "d25a17e9-c624-46be-a0c1-5002b3495c8a",
                        "value": "gyh55799@nezid.com",
                        "verified": false,
                        "via": "email",
                        "status": "sent",
                        "created_at": "2023-12-20T09:42:28.834034Z",
                        "updated_at": "2023-12-20T09:42:28.834034Z"
                    }
                ],
                "recovery_addresses": [
                    {
                        "id": "f45a9abe-9ef3-4761-8936-128e7e3616dc",
                        "value": "gyh55799@nezid.com",
                        "via": "email",
                        "created_at": "2023-12-20T09:42:28.834411Z",
                        "updated_at": "2023-12-20T09:42:28.834411Z"
                    }
                ],
                "metadata_public": null,
                "created_at": "2023-12-20T09:42:28.833549Z",
                "updated_at": "2023-12-20T09:42:28.833549Z",
                "organization_id": null
            },
            "devices": [
                {
                    "id": "0bcffed3-2f65-49d5-87ee-5b401cdfa4a3",
                    "ip_address": "10.42.0.1",
                    "user_agent": "PostmanRuntime/7.36.0",
                    "location": "VN"
                }
            ]
        },
        "identity": {
            "id": "d95ad291-d27b-4eaa-89bb-4c151c757ae6",
            "schema_id": "default",
            "schema_url": "https://kratos.scrapnode.com/schemas/ZGVmYXVsdA",
            "state": "active",
            "state_changed_at": "2023-12-20T09:42:28.832395601Z",
            "traits": {
                "email": "gyh55799@nezid.com"
            },
            "verifiable_addresses": [
                {
                    "id": "d25a17e9-c624-46be-a0c1-5002b3495c8a",
                    "value": "gyh55799@nezid.com",
                    "verified": false,
                    "via": "email",
                    "status": "sent",
                    "created_at": "2023-12-20T09:42:28.834034Z",
                    "updated_at": "2023-12-20T09:42:28.834034Z"
                }
            ],
            "recovery_addresses": [
                {
                    "id": "f45a9abe-9ef3-4761-8936-128e7e3616dc",
                    "value": "gyh55799@nezid.com",
                    "via": "email",
                    "created_at": "2023-12-20T09:42:28.834411Z",
                    "updated_at": "2023-12-20T09:42:28.834411Z"
                }
            ],
            "metadata_public": null,
            "created_at": "2023-12-20T09:42:28.833549Z",
            "updated_at": "2023-12-20T09:42:28.833549Z",
            "organization_id": null
        },
        "continue_with": [
            {
                "action": "show_verification_ui",
                "flow": {
                    "id": "f1b62e42-e311-4222-95cf-bd1f63580cfd",
                    "verifiable_address": "gyh55799@nezid.com",
                    "url": "https://auth.scrapnode.com/verification?flow=f1b62e42-e311-4222-95cf-bd1f63580cfd"
                }
            }
        ]
    }
    ```

    Response Header

    ```
    set-cookie: csrf_token_6c0cb2d8e17be2aeca168648961b34f465bed2ffa37dd7fa2ad8b4867a3b1f15=2rZbiLjhNcHUB9TfVdWIHhfnIr37GPaCqCceyxGNBXo=; Path=/; Domain=scrapnode.com; Max-Age=31536000; HttpOnly; Secure; SameSite=Lax
    set-cookie: ory_kratos_session=MTcwMzA2NTM0OHxGSXMzTTBxUzFiNkVHbkU5M3VZY2RvQVhWN0tQZnVhV291QWEtTGtQYmVzaWNvOTE0VDNmUUk1Yzl2TVl5NGFLanZYYy1HSGRyTmJMLWRqdGpSLVpWR0lTaGhLZ0F4S0dnNzE1OHhLaG03ZVFlOXlLa25qSXNyNEVrcmpRczc0MDA2Z1JCVi1hVkNXczVKVWFvWFptcDdKUElvdG1VQmlmaTlPTHdmdm9OZGZKS295VHIyZDFfcTVxNUtCNEpkSXMwUjZKaFd4QV9UcURmWjk3R2t1cGxTc2F1aGUzNDkwXzJ6NUw4dVBzNWl5cHRJa2h2X3AyVHgtN2owTXJqRGN2bktsYTU1U1VjRnFZam93azdMR3N8uiSW7UHTwl_KR0jaRyEm_sUi5YKKiDh5TxZR11YchJ0=; Path=/; Domain=scrapnode.com; Expires=Thu, 21 Dec 2023 09:42:27 GMT; Max-Age=86399; HttpOnly; Secure; SameSite=Lax
    ```

    In the response body we got the `continue_with` attribute that indicates the registration flow need performing verification step with new flow id

3. Get the verification flow

    An email will be sent to the registration account email, user need to check their inbox and obtain the verification code and back to our UI to complete the verification process. So you need to retrieve the verification flow with the `continue_with[0].flow.id` attribute you received at step #2 to render verification components

    Request

    - Query `id`: obtain from `continue_with[0].flow.id` attribute in response body at step #2

    ```bash
   curl --location 'https://kratos.scrapnode.com/self-service/verification/flows?id=f1b62e42-e311-4222-95cf-bd1f63580cfd' \
    --header 'Accept: application/json' \
    --header 'Cookie: csrf_token_6c0cb2d8e17be2aeca168648961b34f465bed2ffa37dd7fa2ad8b4867a3b1f15=2rZbiLjhNcHUB9TfVdWIHhfnIr37GPaCqCceyxGNBXo=; ory_kratos_session=MTcwMzA2NTM0OHxGSXMzTTBxUzFiNkVHbkU5M3VZY2RvQVhWN0tQZnVhV291QWEtTGtQYmVzaWNvOTE0VDNmUUk1Yzl2TVl5NGFLanZYYy1HSGRyTmJMLWRqdGpSLVpWR0lTaGhLZ0F4S0dnNzE1OHhLaG03ZVFlOXlLa25qSXNyNEVrcmpRczc0MDA2Z1JCVi1hVkNXczVKVWFvWFptcDdKUElvdG1VQmlmaTlPTHdmdm9OZGZKS295VHIyZDFfcTVxNUtCNEpkSXMwUjZKaFd4QV9UcURmWjk3R2t1cGxTc2F1aGUzNDkwXzJ6NUw4dVBzNWl5cHRJa2h2X3AyVHgtN2owTXJqRGN2bktsYTU1U1VjRnFZam93azdMR3N8uiSW7UHTwl_KR0jaRyEm_sUi5YKKiDh5TxZR11YchJ0='
    ```

    Response Body

    ```json
    {
        "id": "f1b62e42-e311-4222-95cf-bd1f63580cfd",
        "type": "browser",
        "expires_at": "2023-12-20T10:42:28.839397Z",
        "issued_at": "2023-12-20T09:42:28.839397Z",
        "request_url": "https://kratos.scrapnode.com/self-service/registration/browser",
        "active": "code",
        "ui": {
            "action": "https://kratos.scrapnode.com/self-service/verification?flow=f1b62e42-e311-4222-95cf-bd1f63580cfd",
            "method": "POST",
            "nodes": [
                {
                    "type": "input",
                    "group": "code",
                    "attributes": {
                        "name": "method",
                        "type": "hidden",
                        "value": "code",
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {}
                },
                {
                    "type": "input",
                    "group": "code",
                    "attributes": {
                        "name": "code",
                        "type": "text",
                        "required": true,
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {
                        "label": {
                            "id": 1070011,
                            "text": "Verification code",
                            "type": "info"
                        }
                    }
                },
                {
                    "type": "input",
                    "group": "code",
                    "attributes": {
                        "name": "method",
                        "type": "submit",
                        "value": "code",
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {
                        "label": {
                            "id": 1070005,
                            "text": "Submit",
                            "type": "info"
                        }
                    }
                },
                {
                    "type": "input",
                    "group": "default",
                    "attributes": {
                        "name": "csrf_token",
                        "type": "hidden",
                        "value": "vbeh+FEMpPO5pIicduwM9ZY77Dvmt/0CodHzW+tCd+tnAfpw6e2RMm2jXEMjOYTrgdzOhh2vC4AJ9u2Q+s9ykQ==",
                        "required": true,
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {}
                }
            ],
            "messages": [
                {
                    "id": 1080003,
                    "text": "An email containing a verification code has been sent to the email address you provided. If you have not received an email, check the spelling of the address and make sure to use the address you registered with.",
                    "type": "info"
                }
            ]
        },
        "state": "sent_email"
    }
    ```

4. Verify the registration account email

    In this example, we are going to use the code `489496` to demonstrate the flow

    Request

    - Query `flow`: obtain from the `continue_with[0].flow.id` attribute in the response body in step #2 (value: `44db86dc-131f-47c6-ae2b-38981a686d45`)
    - Body `code`: User input

    ```bash
    curl --location 'https://kratos.scrapnode.com/self-service/verification?flow=f1b62e42-e311-4222-95cf-bd1f63580cfd' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --header 'Accept: application/json' \
    --header 'Cookie: csrf_token_6c0cb2d8e17be2aeca168648961b34f465bed2ffa37dd7fa2ad8b4867a3b1f15=2rZbiLjhNcHUB9TfVdWIHhfnIr37GPaCqCceyxGNBXo=; ory_kratos_session=MTcwMzA2NTM0OHxGSXMzTTBxUzFiNkVHbkU5M3VZY2RvQVhWN0tQZnVhV291QWEtTGtQYmVzaWNvOTE0VDNmUUk1Yzl2TVl5NGFLanZYYy1HSGRyTmJMLWRqdGpSLVpWR0lTaGhLZ0F4S0dnNzE1OHhLaG03ZVFlOXlLa25qSXNyNEVrcmpRczc0MDA2Z1JCVi1hVkNXczVKVWFvWFptcDdKUElvdG1VQmlmaTlPTHdmdm9OZGZKS295VHIyZDFfcTVxNUtCNEpkSXMwUjZKaFd4QV9UcURmWjk3R2t1cGxTc2F1aGUzNDkwXzJ6NUw4dVBzNWl5cHRJa2h2X3AyVHgtN2owTXJqRGN2bktsYTU1U1VjRnFZam93azdMR3N8uiSW7UHTwl_KR0jaRyEm_sUi5YKKiDh5TxZR11YchJ0=; affinity=96de19e86d5da9d1' \
    --data-urlencode 'code=489496' \
    --data-urlencode 'csrf_token=vbeh+FEMpPO5pIicduwM9ZY77Dvmt/0CodHzW+tCd+tnAfpw6e2RMm2jXEMjOYTrgdzOhh2vC4AJ9u2Q+s9ykQ=='
    ```

    Response Body

    ```json
    {
        "id": "f1b62e42-e311-4222-95cf-bd1f63580cfd",
        "type": "browser",
        "expires_at": "2023-12-20T10:42:28.839397Z",
        "issued_at": "2023-12-20T09:42:28.839397Z",
        "request_url": "https://kratos.scrapnode.com/self-service/registration/browser",
        "active": "code",
        "ui": {
            "action": "https://auth.scrapnode.com/",
            "method": "GET",
            "nodes": [
                {
                    "type": "input",
                    "group": "default",
                    "attributes": {
                        "name": "csrf_token",
                        "type": "hidden",
                        "value": "XbFBP/PHARIDAaxTKsfj/bWU0O/0HRTVSPhZOH99fNKHBxq3SyY009cGeIx/EmvjonPyUg8F4lfg30fzbvB5qA==",
                        "required": true,
                        "disabled": false,
                        "node_type": "input"
                    },
                    "messages": [],
                    "meta": {}
                },
                {
                    "type": "a",
                    "group": "code",
                    "attributes": {
                        "href": "https://auth.scrapnode.com/",
                        "title": {
                            "id": 1070009,
                            "text": "Continue",
                            "type": "info"
                        },
                        "id": "continue",
                        "node_type": "a"
                    },
                    "messages": [],
                    "meta": {
                        "label": {
                            "id": 1070009,
                            "text": "Continue",
                            "type": "info"
                        }
                    }
                }
            ],
            "messages": [
                {
                    "id": 1080002,
                    "text": "You successfully verified your email address.",
                    "type": "success"
                }
            ]
        },
        "state": "passed_challenge"
    }
    ```

#### Login


