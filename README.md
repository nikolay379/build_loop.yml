

# build_loop.yml
steps:
  - script: create-user.sh 'john'
    displayName: 'Create user <john>'
  - script: create-user.sh 'jane'
    displayName: 'Create user <jane>'
  - script: create-user.sh 'bob'
    displayName: 'Create user <bob>'
  - script: grant-database-access.sh 'john'
    displayName: 'Grant database access to <john>'
  - script: grant-database-access.sh 'jane'
    displayName: 'Grant database access to <jane>'
  - script: grant-database-access.sh 'bob'
    displayName: 'Grant database access to <bob>'
  - script: grant-datafactory-access.sh 'john'
    displayName: 'Grant Data Factory access to <john>'
  - script: grant-datafactory-access.sh 'jane'
    displayName: 'Grant Data Factory access to <jane>'
  - script: grant-datafactory-access.sh 'bob'
    displayName: 'Grant Data Factory access to <bob>'  
parameters:
  - name: users
    type: object
    default:
      - john
      - jane
      - bob
      -   - ${{ each user in parameters.users }}:
    - script: create-user.sh ${{ user }}
      displayName: 'Create user ${{ user }}'
    - script: grant-database-access.sh ${{ user }}
      displayName: 'Grant database access to ${{ user }}'
    - script: grant-datafactory-access.sh ${{ user }}
      displayName: 'Grant Data Factory access to ${{ user }}'  
parameters:
  - name: users
    type: object
    default:
      - john
      - jane
      - bob

steps:
  - ${{ each user in parameters.users }}:
    - script: create-user.sh ${{ user }}
      displayName: 'Create user ${{ user }}'
    - script: grant-database-access.sh ${{ user }}
      displayName: 'Grant database access to ${{ user }}'
    - script: grant-datafactory-access.sh ${{ user }}
      displayName: 'Grant Data Factory access to ${{ user }}'  
parameters:
  - name: users
    type: object
    default:
      - name: 'john'
        email: 'john@doe.com'
      - name: 'jane'
        email: 'jane@doe.com'
      - bob

steps:
  - ${{ each user in parameters.users }}:
    - ${{ if eq(user.name, '') }}:
      - script: echo 'User ${{ user }} has no email.'
    - ${{ if ne(user.name, '') }}:
      - script: echo 'User ${{ user.name }} with email ${{ user.email }}.'
variables:
  environment_name: Development
  environment_code: d
variables:
  environment_name: Production
  environment_code: p
parameters:
  - name: targets
    type: object
    default:
      - dev
      - prod

jobs:
  - ${{ each target in parameters.targets }}:
    - job:
      displayName: 'Deploy ${{ target }}'
      variables:
        - template: vars/${{ target }}.yml
      steps:
        - script: echo "I am doing this on ${{ target }}"
