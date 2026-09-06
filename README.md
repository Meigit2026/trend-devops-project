# Trendify - DevOps Application Deployment

## Project Overview

Trendify is a production-ready React web application deployed using a complete DevOps workflow on AWS.



The project demonstrates containerization, infrastructure provisioning, CI/CD automation, Kubernetes deployment, GitHub webhook integration, AWS Elastic Load Balancing, and open-source monitoring using Prometheus and Grafana.



\## Architecture



```text

Developer

&#x20;   |

&#x20;   v

GitHub Repository

&#x20;   |

&#x20;   | GitHub Push

&#x20;   v

GitHub Webhook

&#x20;   |

&#x20;   v

Jenkins on AWS EC2

&#x20;   |

&#x20;   +---- Docker Build

&#x20;   |

&#x20;   +---- DockerHub Push

&#x20;   |

&#x20;   +---- Configure EKS

&#x20;   |

&#x20;   +---- kubectl Deploy

&#x20;   |

&#x20;   v

Amazon EKS

&#x20;   |

&#x20;   +---- Trendify Deployment

&#x20;   |       |

&#x20;   |       +---- Pod 1

&#x20;   |       |

&#x20;   |       +---- Pod 2

&#x20;   |

&#x20;   v

Kubernetes LoadBalancer

&#x20;   |

&#x20;   v

Public Trendify Application



Monitoring:

Amazon EKS

&#x20;   |

&#x20;   +---- Prometheus

&#x20;   +---- Node Exporter

&#x20;   +---- kube-state-metrics

&#x20;   |

&#x20;   v

Grafana

