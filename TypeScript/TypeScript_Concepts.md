### TypeScript Concepts
##### 1. Type Annotations
```typescript
//the type is followed by :
const name: string = '';
//| is union type says the variable can only be one of those values or null
//the =null part is just to initialize the variable with null
const reasoningEffort: 'low' | 'medium' | 'high' | null = null;
// if used in function, it means the function's return type. Below function return type is number
function add(a: number, b: number): number {
    return a + b;
}


```
##### 2. Interfaces - define the shape of obj
- Example 1
```typescript
//define the shape of ModifyAssistant obj

interface ModifyAssistantRequest {
  //? means that property is optional
  name?: string;
  description?: string;
  instructions?: string;
  // other properties...
}
```
- Example 2 : we can define an interface and use it in the other
```typescript
interface DomainSetting { //define DomainSetting object
  domain: string;
  isRespected: boolean;
}

interface RobotsTxtPreferenceModalProps {
  isOpen: boolean;
  onClose: () => void; //means onClose is a func that takes no param and returns undefined
  onConfirm: () => void;
  domains: string[]; 
  domainSettings: DomainSetting[]; //use DomainSetting here
  
  //it takes an array of Domainsetting type and return undefined
  onDomainSettingsChange: (settings: DomainSetting[]) => void; 
}
```
##### 3.Generics
```typescript
//tells that this useState hook works with string
const [name, setName] = useState<string>('');
const [uniqueDomains, setUniqueDomains] = useState<string[]>([]);
//tells what type the modifyRequest gonnabe
const modifyRequest = useMemo<returnType>(() => {...}, [...]);
```
`useState`and `useMemo`is generic functions and `<>` defineds the type

```typescript
function useMemo<T>(factory: () => T, deps: DependencyList): T;
```
- `<T>`: This is a generic type parameter, meaning useMemo can work with any type.

- `factory`: () => T: The function you pass to useMemo must return something of type T.

- `deps`: DependencyList: The dependency array that determines when to recompute.

- `: T`: This means the function itself returns a value of `T`

### React Function Components and TypeScript Generics
```typescript
interface RobotsTxtPreferenceModalProps {
    isOpen: boolean;
    onClose: () => void;
    onConfirm: () => void;
    domains: string[];
    selectedDomains: string[];
    onDomainsChange: (domains: string[]) => void;
}

const RobotsTxtPreferenceModal: React.FC<RobotsTxtPreferenceModalProps> = ({
  //this is parameter descructuring
    isOpen, onClose, onConfirm, domains, selectedDomains, onDomainsChange
}) => {
    const handleDomainToggle = (domain: string) => {
        if (selectedDomains.includes(domain)) {
            onDomainsChange(selectedDomains.filter((d: string) => d !== domain));
        } else {
            onDomainsChange([...selectedDomains, domain]);
        }
    }
       
    return (
        <Modal isOpen={isOpen} toggle={onClose}>
            <ModalHeader toggle={onClose}>Robots.txt Preferences</ModalHeader>
            <ModalBody>
                <p>Select domains to respect robots.txt rules:</p>
                {domains.map((domain: string) => (
                    <FormGroup check key={domain}>
                        <Label check>
                            <Input type="checkbox"
                                checked={selectedDomains.includes(domain)}
                                onChange={() => handleDomainToggle(domain)}
                            />
                            {domain}
                        </Label>
                    </FormGroup>
                ))}
            </ModalBody>
            <ModalFooter>
                <Button color="secondary" onClick={onClose}>
                    Cancel
                </Button>
                <Button color="primary" onClick={onConfirm}>
                    Confirm
                </Button>
            </ModalFooter>
        </Modal>
    )
}

export default RobotsTxtPreferenceModal;
```

1. The `React.FC`Type
React.FC stands for "React Function Component". It's a TypeScript type provided by React that represents a function component.
When you write `React.FC`, you're telling TypeScript: "this variable will hold a React function component."
2. Generics with `<RobotsTxtPreferenceModalProps>`
    - The angle brackets `<RobotsTxtPreferenceModalProps>` are using TypeScript's generic syntax. Generics allow you to create reusable components that can work with different types while maintaining type safety.
    - In this case, `React.FC` is a generic type that accepts a type parameter which specifies what props the component accepts. By writing `React.FC<RobotsTxtPreferenceModalProps>`, you're saying:
  "This is a React function component that accepts props of type RobotsTxtPreferenceModalProps."
    - It's similar to how arrays work with generics - if you have Array<string>, it's an array of strings. Here you have React.FC<RobotsTxtPreferenceModalProps>, which is a function component that takes props of type RobotsTxtPreferenceModalProps.